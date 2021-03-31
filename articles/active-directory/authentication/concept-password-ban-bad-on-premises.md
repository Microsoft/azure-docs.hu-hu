---
title: Azure AD jelszavas védelem – Azure Active Directory
description: Gyenge jelszavak betiltása a helyszíni Active Directory Domain Services környezetekben az Azure AD jelszavas védelme használatával
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5df1cb158821fb0cd85d90f9ba3b79d80adf45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743921"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>A helyszíni Azure AD-beli jelszavas védelem betartatása Active Directory Domain Services

Az Azure AD jelszavas védelme észleli és blokkolja az ismert gyenge jelszavakat és azok változatait, valamint blokkolhatja a szervezete számára jellemző további gyenge kifejezéseket is. Az Azure AD jelszavas védelem helyszíni üzembe helyezése ugyanazokat a globális és egyéni tiltott jelszavakat használja, amelyek az Azure AD-ben vannak tárolva, és ugyanazokat a helyszíni jelszavakat ellenőrzi, mint az Azure AD a felhőalapú módosításokhoz. Ezeket az ellenőrzéseket a rendszer a jelszó módosításakor és a jelszó-visszaállítási események során hajtja végre a helyszíni Active Directory Domain Services (AD DS) tartományvezérlőkön.

## <a name="design-principles"></a>Tervezési alapelvek

Az Azure AD jelszavas védelmet a következő elvekkel tervezték szem előtt tartva:

* A tartományvezérlőknek (DCs) soha nem kell közvetlenül kommunikálnia az internettel.
* Nincsenek új hálózati portok megnyitva a tartományvezérlőn.
* Nincs szükség AD DS séma módosítására. A szoftver a meglévő AD DS *tároló* -és *serviceConnectionPoint* séma-objektumokat használja.
* Nincs szükség minimális AD DS tartományra vagy erdő működési szintjére (DFL/FFL).
* A szoftver nem hoz létre vagy igényel fiókokat az általa védett AD DS-tartományokban.
* Felhasználó tiszta – a szöveges jelszavak soha nem hagyják el a tartományvezérlőt a jelszó-érvényesítési műveletek során vagy bármilyen más időpontban.
* A szoftver nem függ más Azure AD-funkcióktól. Például az Azure AD Password hash Sync (PHS) nem kapcsolódik vagy nem szükséges az Azure AD jelszavas védelméhez.
* A növekményes üzembe helyezés támogatott, azonban a jelszóházirend csak abban az esetben kényszerítve van, ha a tartományvezérlő ügynöke telepítve van.

## <a name="incremental-deployment"></a>Növekményes üzembe helyezés

Az Azure AD jelszavas védelme támogatja a tartományvezérlők közötti növekményes telepítést egy AD DS tartományban. Fontos megérteni, hogy mit jelent ez valójában, és mi a kompromisszumok.

Az Azure AD jelszavas védelem DC-ügynök szoftvere csak akkor tudja érvényesíteni a jelszavakat, ha a TARTOMÁNYVEZÉRLŐre van telepítve, és csak az adott TARTOMÁNYVEZÉRLŐre eljuttatott jelszó-módosításokra. A felhasználói jelszó módosításainak feldolgozásához a Windows-ügyfélszámítógépek által kiválasztott tartományvezérlőket nem lehet szabályozni. Az egységes és az Azure AD jelszavas védelem biztonsági kényszerítésének garantálása érdekében a tartományvezérlői ügynök szoftverét a tartomány összes tartományvezérlőjén telepíteni kell.

Számos szervezet szeretné alaposan tesztelni az Azure AD jelszavas védelmét a tartományvezérlők egy részhalmazán a teljes üzembe helyezés előtt. A forgatókönyv támogatása érdekében az Azure AD jelszavas védelme támogatja a részleges üzembe helyezést. Az adott TARTOMÁNYVEZÉRLŐn lévő DC-ügynök szoftvere aktívan ellenőrzi a jelszavakat akkor is, ha a tartományban lévő más tartományvezérlők nem telepítették a tartományvezérlő-ügynök szoftverét. Az ilyen típusú részleges központi telepítések nem biztonságosak, és nem ajánlott tesztelési célokra.

## <a name="architectural-diagram"></a>Építészeti diagram

Az Azure AD jelszavas védelem helyszíni AD DS környezetben való üzembe helyezése előtt fontos megérteni a mögöttes tervezési és működési fogalmakat. Az alábbi ábra bemutatja, hogyan működik együtt az Azure AD jelszavas védelem összetevői:

![Hogyan működnek együtt az Azure AD jelszavas védelmi összetevői?](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Az Azure AD jelszavas védelem proxy szolgáltatása az aktuális AD DS erdőben található bármely tartományhoz csatlakoztatott gépen fut. A szolgáltatás elsődleges célja, hogy továbbítsa a szabályzatok letöltési kérelmeit a DCs-ből az Azure AD-be, majd visszaadja az Azure AD válaszait a TARTOMÁNYVEZÉRLŐnek.
* A DC-ügynök jelszavas szűrő DLL-je fogadja a felhasználói jelszó-érvényesítési kérelmeket az operációs rendszertől. A szűrő továbbítja azokat a DC Agent szolgáltatásnak, amely helyileg fut a TARTOMÁNYVEZÉRLŐn.
* Az Azure AD jelszavas védelem DC Agent szolgáltatása a DC-ügynök jelszavas szűrő DLL-fájljának jelszavas ellenőrzési kérelmeit fogadja. A DC Agent szolgáltatás az aktuális (helyileg elérhető) jelszóházirend használatával dolgozza fel azokat, és visszaadja az *pass* vagy a *Fail* eredményét.

## <a name="how-azure-ad-password-protection-works"></a>Az Azure AD jelszavas védelem működése

A helyszíni Azure AD jelszavas védelem összetevői a következőképpen működnek:

1. Az egyes Azure AD-alapú jelszavas védelmi proxy szolgáltatási példányok az erdőben lévő tartományvezérlők számára hirdetik meg a *serviceConnectionPoint* objektum létrehozásával Active Directory.

    Az Azure AD jelszavas védelem minden egyes DC Agent szolgáltatása egy *serviceConnectionPoint* objektumot is létrehoz Active Directoryban. Ez az objektum elsősorban jelentéskészítésre és diagnosztika használatára szolgál.

1. A DC Agent szolgáltatás feladata, hogy kezdeményezzen egy új jelszóházirend letöltését az Azure AD-ből. Az első lépés egy Azure AD-beli jelszavas védelmi proxy szolgáltatás megkeresése az erdő *serviceConnectionPoint* -objektumok számára történő lekérdezésével.

1. Ha talál rendelkezésre álló proxy szolgáltatást, a DC ügynök egy jelszóházirend letöltési kérelmét küldi el a proxy szolgáltatásnak. A proxy szolgáltatás pedig elküldi a kérést az Azure AD-nek, majd visszaadja a DC Agent szolgáltatásnak küldött választ.

1. Miután a DC Agent szolgáltatás új jelszót kapott az Azure AD-től, a szolgáltatás a tartomány *SYSVOL* mappa-megosztásának gyökerében lévő dedikált mappában tárolja a szabályzatot. A DC Agent szolgáltatás a mappában is figyeli ezt a mappát abban az esetben, ha újabb házirendek replikálódnak a tartomány más DC-ügynök szolgáltatásaiból.

1. A DC Agent szolgáltatás mindig új házirendet kér a szolgáltatás indításakor. A DC Agent szolgáltatás elindítása után óránként ellenőrzi a jelenlegi helyileg elérhető házirend korát. Ha a házirend egy óránál régebbi, a tartományvezérlő ügynöke új szabályzatot kér az Azure AD-től a proxy szolgáltatáson keresztül a korábban leírtak szerint. Ha az aktuális házirend nem régebbi, mint egy óra, a tartományvezérlő ügynök továbbra is ezt a házirendet használja.

1. Ha egy tartományvezérlő a jelszó-módosítási eseményeket fogadja, a rendszer a gyorsítótárazott házirend alapján határozza meg, hogy az új jelszót elfogadták vagy elutasították-e.

### <a name="key-considerations-and-features"></a>Főbb szempontok és funkciók

* Amikor az Azure AD jelszavas védelemre vonatkozó jelszóházirend le van töltve, a szabályzat egy bérlőre vonatkozik. Más szóval a jelszó-szabályzatok mindig a Microsoft globális betiltott jelszavas listájának és a bérlői egyéni tiltott jelszó-lista kombinációja.
* A tartományvezérlő ügynök a proxy szolgáltatással kommunikál a TCP-n keresztüli RPC protokollon keresztül. A proxy szolgáltatás a konfigurációtól függően egy dinamikus vagy statikus RPC-porton figyeli ezeket a hívásokat.
* A DC-ügynök soha nem figyeli a hálózatról elérhető portot.
* A proxy szolgáltatás soha nem hívja meg a DC Agent szolgáltatást.
* A proxy szolgáltatás állapota állapot nélküli. Soha nem gyorsítótárazza a házirendeket vagy az Azure-ból letöltött más állapotot.
* A DC Agent szolgáltatás mindig a legutóbbi helyileg elérhető jelszóházirend használatával értékeli ki a felhasználó jelszavát. Ha a helyi TARTOMÁNYVEZÉRLŐn nem érhető el jelszóházirend, a rendszer automatikusan elfogadja a jelszót. Ha ez történik, a rendszer egy eseményt naplóz, hogy figyelmeztesse a rendszergazdát.
* Az Azure AD jelszavas védelme nem valós idejű házirend-alkalmazás motorja. A jelszó-házirend konfigurációjának módosítása az Azure AD-ben, illetve a változás elérésekor és az összes tartományvezérlőn való betartatása között eltelt idő lehet.
* Az Azure AD jelszavas védelme kiegészíti a meglévő AD DS jelszóházirend-szabályzatokat, nem pedig helyettesíti azokat. Ebbe beletartozik minden más, a külső féltől származó jelszó-szűrési dll-fájl, amely telepíthető lehet. AD DS mindig megköveteli, hogy az összes jelszó-ellenőrzési összetevő elfogadja a jelszó elfogadását.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Erdő/bérlői kötés az Azure AD jelszavas védelméhez

Az Azure AD jelszavas védelem AD DS erdőben való üzembe helyezéséhez az adott erdőnek az Azure AD-vel való regisztrációja szükséges. Az üzembe helyezett proxy szolgáltatásoknak is regisztrálva kell lenniük az Azure AD-ben. Ezek az erdő-és proxy-regisztrációk egy adott Azure AD-bérlőhöz vannak társítva, amely implicit módon azonosítható a regisztráció során használt hitelesítő adatokkal.

Az erdőben lévő AD DS erdőt és az összes telepített proxy szolgáltatást ugyanahhoz a bérlőhöz kell regisztrálni. Nem támogatott, hogy AD DS erdőben vagy bármely proxy szolgáltatásban legyen regisztrálva az adott erdőben különböző Azure AD-bérlők számára. Az ilyen helytelenül konfigurált központi telepítés tünetei közé tartozik a jelszóházirendek letöltésének képtelensége.

> [!NOTE]
> Azoknak az ügyfeleknek, amelyeknek több Azure AD-Bérlővel kell rendelkezniük, az egyes erdők Azure AD-beli jelszavas védelemhez való regisztrálásához egy megkülönböztető bérlőt kell választaniuk.

## <a name="download"></a>Letöltés

Az Azure AD jelszavas védelem két szükséges ügynök-telepítője a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=57071)érhető el.

## <a name="next-steps"></a>Következő lépések

A helyszíni Azure AD jelszavas védelem használatának megkezdéséhez kövesse az alábbi útmutatókat:

> [!div class="nextstepaction"]
> [Helyszíni Azure AD jelszavas védelem üzembe helyezése](howto-password-ban-bad-on-premises-deploy.md)
