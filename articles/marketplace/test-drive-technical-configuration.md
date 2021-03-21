---
title: A test Drive technikai konfigurációja, a Microsoft kereskedelmi piactér
description: További információ a tesztelési meghajtókról. A tesztelési meghajtók lehetővé teszik az új ügyfelek számára az ajánlat tesztelését, mielőtt véglegesítik a vásárlást.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 553eba3898a5ea42e5d478603e35e82c68abcab1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462897"
---
# <a name="test-drive-technical-configuration"></a>Tesztverzió műszaki konfigurálása

A Microsoft kereskedelmi piactéren a test Drive lehetőséggel beállíthatók a termék főbb szolgáltatásainak gyakorlati és saját irányítású bemutatói. A tesztelési meghajtóval az új ügyfelek az ajánlat megvásárlása előtt kipróbálhatják az ajánlatot. További információ: [Mi az a test Drive?](what-is-test-drive.md)

Ha már nem szeretne tesztelési meghajtót biztosítani az ajánlatához, térjen vissza az **ajánlat beállítása** lapra, és törölje a **Test Drive engedélyezése** jelölőnégyzet bejelölését. Nem minden ajánlat típusa rendelkezik elérhető tesztelési meghajtóval.

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager tesztelési meghajtó

Ez az egyetlen tesztelési lehetőség a virtuális gépekhez vagy az Azure-alkalmazások ajánlatához, és meglehetősen részletes beállításra van szükség. Olvassa el az alábbi szakaszokat a [központi telepítési előfizetés részletei](#deployment-subscription-details) és a [tesztelési meghajtók listájában](#test-drive-listings), majd folytassa a külön témakörben [Azure Resource Manager a tesztelési meghajtók konfigurációját](azure-resource-manager-test-drive.md).

## <a name="hosted-test-drive"></a>Szolgáltatott tesztelési meghajtó

A Microsoft el tudja távolítani a tesztelési meghajtó beállításának összetettségét a szolgáltatás kiépítésének és megszüntetésének üzemeltetésével és karbantartásával. Az ilyen típusú tesztelési meghajtó konfigurációja ugyanaz, függetlenül attól, hogy a tesztvezetés a Dynamics 365 Customer engagement vagy a Dynamics 365 művelet célközönségét célozza-e.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező) – állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Az egyidejű felhasználók egy Dynamics 365-licencet használnak, amíg a tesztvezetés aktív, ezért győződjön meg arról, hogy elegendő licenc áll rendelkezésre a maximálisan engedélyezett készlet támogatásához. Az ajánlott érték 3-5.

- **Tesztelési meghajtó időtartama** (kötelező) – Itt adhatja meg, hogy hány óra elteltével maradjon aktív a tesztvezetés az egyes ügyfelek számára. Ezen időszak után a munkamenet véget ért, és már nem fogja használni az egyik licencét. Az ajánlat bonyolultsága alapján 2-24 óra értékű értéket ajánlunk. Ez az időtartam csak egész órában állítható be (például "2 óra"). "1,5 óra" nem). Ha elfogynak az idő, a felhasználó új munkamenetet igényelhet, és újra el szeretné érni a teszt meghajtót.

- **Példány URL-címe** (kötelező) – az az URL-cím, amelyben az ügyfél elkezdi a tesztelési meghajtót. Általában a Dynamics 365-példány URL-címe, amely az alkalmazást az alkalmazással együtt futtatja (például: `https://testdrive.crm.dynamics.com` ).

- **Példány webes API URL-címe** (kötelező) – a Dynamics 365-példány webes API URL-címének lekéréséhez jelentkezzen be a Microsoft 365-fiókjába, és navigáljon a **Beállítások**  >  **testreszabása**  >  **fejlesztői erőforrások**  >  **példány webes API (szolgáltatás gyökerének URL-címe)** mezőbe, és másolja az itt található URL-címet (például: `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Szerepkör neve** (kötelező) – adja meg az egyéni Dynamics 365-tesztelési meghajtóban definiált biztonsági szerepkör nevét, amely a felhasználóhoz lesz hozzárendelve a tesztelési meghajtón (például Test-Drive-role).

A Dynamics 365-környezet tesztelési meghajtóhoz való beállításával és a AppSource engedélyek megadásával kapcsolatos segítségért kövesse az [alábbi utasításokat](./test-drive-azure-subscription-setup.md).

Az üzemeltetett tesztelési meghajtó listázásával és konfigurálásával kapcsolatos részletes utasításokért tekintse meg az [üzemeltetett tesztelési meghajtó részletes konfigurációját](./test-drive-hosted-detailed-config.md) .

## <a name="logic-app-test-drive"></a>Logic app Test Drive

Ezt a típusú tesztelési meghajtót nem a Microsoft üzemelteti. Használatával kapcsolódhat egy Dynamics 365-ajánlathoz vagy más egyéni erőforráshoz, amely számos összetett megoldási architektúrát magában foglal. A Logic app test Drives beállításával kapcsolatos további információkért látogasson el a [műveletekre](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) és az [ügyfelek részvételére](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) a githubon.

- **Régió** (kötelező, egyetlen kiválasztható legördülő lista) – jelenleg 26 Azure által támogatott régió érhető el, ahol a tesztvezetés elérhetővé tehető. A logikai alkalmazás erőforrásai a kiválasztott régióba lesznek telepítve. Ha a logikai alkalmazás egy adott régióban tárolt egyéni erőforrásokkal rendelkezik, győződjön meg arról, hogy a régió itt van kiválasztva. A legjobb módszer a logikai alkalmazás helyi üzembe helyezése az Azure-előfizetésben a portálon, és annak ellenőrzése, hogy megfelelően működik-e a kijelölés előtt.

- Egyidejű **tesztelési meghajtók maximális** száma (kötelező) – állítsa be a tesztelési meghajtót egyszerre használó ügyfelek maximális számát. Ezek a tesztelési meghajtók már telepítve vannak, és lehetővé teszik, hogy az ügyfelek azonnal hozzáférjenek az üzembe helyezésre való várakozás nélkül.

- **Tesztelési meghajtó időtartama** (kötelező) – Itt adhatja meg, hogy a tesztvezetés hány óra alatt maradjon aktív állapotban. A tesztelési meghajtó automatikusan leáll az adott időszak lejárta után.

- **Azure-erőforráscsoport neve** (kötelező) – Itt adhatja meg az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md#resource-groups)nevét, ahol a logikai alkalmazás tesztelési meghajtója mentve lesz.

- **Azure Logic App-alkalmazás neve** (kötelező) – Itt adhatja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót hozzárendeli a felhasználóhoz. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

- **Logikai alkalmazás nevének** megszüntetése (kötelező) – Itt adhatja meg annak a logikai alkalmazásnak a nevét, amely a tesztelési meghajtót kiépíti az ügyfél befejeződése után. Ezt a logikai alkalmazást a fenti Azure-erőforrások csoportba kell menteni.

## <a name="power-bi-test-drive"></a>Power BI tesztelési meghajtó

Azok a termékek, amelyek interaktív Power BI vizualizációt szeretnének bemutatni, használhatnak egy beágyazott hivatkozást, amellyel megoszthatják az egyéni kialakítású irányítópultokat a tesztelési meghajtóként, és nincs szükség további technikai konfigurációra. Itt fel kell töltenie a beágyazott Power BI URL-címét.

Power BI alkalmazások beállításával kapcsolatos további információkért lásd: [Mi a Power bi alkalmazás?](/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>Központi telepítési előfizetés részletei

Annak engedélyezéséhez, hogy a Microsoft a tesztelési meghajtót az Ön nevében telepítse, hozzon létre és adjon meg egy különálló, egyedi Azure-előfizetést (Power BI tesztelési meghajtókhoz nem szükséges).

- **Azure-előfizetés azonosítója** (Azure Resource Manager és Logic apps esetén szükséges) – adja meg az előfizetés azonosítóját, hogy hozzáférést biztosítson az Azure-fiók szolgáltatásaihoz az erőforrás-használat jelentéskészítéséhez és számlázásához. Javasoljuk, hogy [hozzon létre egy külön Azure-előfizetést](../cost-management-billing/manage/create-subscription.md) , amelyet tesztelési meghajtókhoz kíván használni, ha még nem rendelkezik ilyennel. Az Azure-előfizetésének AZONOSÍTÓját a [Azure Portalba](https://portal.azure.com/) való bejelentkezéssel és a bal oldali menü **előfizetések** lapján érheti el. A lap kiválasztásával megjelenítheti az előfizetés-AZONOSÍTÓját (például: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure ad-bérlő azonosítója** (kötelező) – adja meg a Azure Active Directory (ad) [bérlői azonosítóját](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Tulajdonságok** elemet, majd keresse **meg a listában** szereplő 50c464d3-4930-494c-963c-1e951d15360e (például:). A szervezet bérlői AZONOSÍTÓját a tartománynév címe alapján is megkeresheti a következő helyen: [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Azure ad-bérlő neve** (dinamikus 365 esetén szükséges) – adja meg a Azure Active Directory (ad) nevét. A név megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), a jobb felső sarokban a bérlő neve a fiók neve alatt jelenik meg.

- **Azure ad** -alkalmazás azonosítója (kötelező) – adja meg a Azure Active Directory (ad-) [alkalmazás azonosítóját](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)). Az azonosító megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), válassza a Active Directory fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk** lehetőséget, majd keresse meg a listában szereplő **alkalmazás-azonosító** számát (például `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure ad-alkalmazás ügyfél-titka** (kötelező) – adja meg az Azure ad-alkalmazás [ügyfelének titkos kulcsát](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret). Az érték megkereséséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/). Válassza ki a **Azure Active Directory** fület a bal oldali menüben, válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki a test Drive alkalmazást. Ezután válassza a **tanúsítványok és titkos kulcsok** lehetőséget, válassza az **új ügyfél titka** elemet, írja be a leírást, válassza a **soha nem** **jár le**, és válassza a **Hozzáadás** lehetőséget. Ügyeljen rá, hogy az értéket másolja le. Az érték másolása előtt ne navigáljon el az oldalról.

## <a name="test-drive-listings"></a>Tesztelési meghajtók listázása

A a partner központ **Test Drive (tesztelési** meghajtó) lapján található tesztelési **meghajtók listázása** lehetőség megjeleníti azokat a nyelveket (és piacokat), ahol a tesztelési meghajtó elérhető, jelenleg az angol (Egyesült Államok) az egyetlen elérhető hely. Ezen felül az oldal megjeleníti a nyelvspecifikus lista állapotát és a hozzáadott dátumot és időt. Meg kell határoznia a tesztvezetés részleteit (Leírás, felhasználói kézikönyv, videók stb.) az egyes nyelvekhez/piacokhoz.

- **Leírás** (kötelező): írja le a tesztelési meghajtót, hogy mit kell bemutatni, a felhasználó által a kísérlethez szükséges célokat, a felderített funkciókat, valamint minden olyan releváns információt, amellyel a felhasználó eldöntheti, hogy szeretné-e beszerezni az ajánlatot. Ebben a mezőben legfeljebb 3 000 karaktert lehet megadni.

- **Hozzáférési információk** (Azure Resource Manager és logikai tesztelési meghajtókhoz szükséges): ismertesse, hogy az ügyfélnek milyen információra van szüksége ahhoz, hogy hozzáférhessen és használhassa ezt a teszt meghajtót. Tekintse át az ajánlat használatát, és pontosan azt, amit az ügyfélnek tudnia kell a szolgáltatások eléréséhez a tesztelési meghajtón. Ebben a mezőben legfeljebb 10 000 karaktert lehet megadni.

- **Felhasználói kézikönyv** (kötelező): részletes útmutató a test Drive-élményhez. A felhasználói kézikönyvnek pontosan le kell fednie, hogy mit szeretne kapni az ügyféltől a tesztelési meghajtón, és az esetlegesen felmerülő kérdésekre mutató hivatkozásként szolgáljon. A fájlnak PDF formátumúnak kell lennie, és a feltöltés után a neve (255 karakter max).

- **Videók: videók hozzáadása** (nem kötelező): a máshol üzemeltetett videók hivatkozással és miniatűr képpel (533 x 324 képpont) jelennek meg, így az ügyfelek megtekinthetik az információk részletes ismertetését, így könnyebben megismerhetik a tesztelési meghajtót, beleértve az ajánlat funkcióinak sikeres használatát, valamint az előnyeiket kiemelő forgatókönyvek megismerését.
  - **Név** (kötelező)
  - **URL-cím** (csak YouTube vagy Vimeo esetén szükséges)
  - **Miniatűr** (533 x 324 képpont) – a képnek png formátumúnak kell lennie.

Ha jelenleg a partner Centerben hozza létre a tesztelési meghajtót, a folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

Az üzemeltetett tesztelési meghajtó listázásával és konfigurálásával kapcsolatos részletes utasításokért tekintse meg az [üzemeltetett tesztelési meghajtó részletes konfigurációját](./test-drive-hosted-detailed-config.md) .

## <a name="additional-resources"></a>További források

## <a name="next-steps"></a>Következő lépések

- [Tesztelési meghajtó – ajánlott eljárások](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Áttekintés](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása)
- [Meglévő ajánlat frissítése a kereskedelmi piactéren](partner-center-portal/update-existing-offer.md)