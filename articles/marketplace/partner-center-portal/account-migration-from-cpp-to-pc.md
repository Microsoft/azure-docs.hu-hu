---
title: Fiókok áttelepíthetők a Cloud Partner Portalról a Microsoft kereskedelmi piactérről
description: Ismerje meg, hogyan migrálhatja fiókját Cloud Partner Portalról a Microsoft kereskedelmi piactéren az Azure-ba
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 09/23/2019
ms.openlocfilehash: 9e9f3b7ea3d99de51b9398bda82b690d2c249a1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92520965"
---
# <a name="how-to-migrate-your-account-from-cloud-partner-portal-to-partner-center"></a>Fiók áttelepítése Cloud Partner Portalról a partneri központba

Ha meglévő Cloud Partner Portal-(CPP-) fiókkal rendelkezik, a fiók beállításait át kell telepíteni a fiókpartner-központba.

## <a name="account-migration-process"></a>Fiók áttelepítési folyamata

Ha egy adott fiókhoz tartozó tulajdonosi szerepkörrel rendelkező felhasználó, a közzétevő profil lapján megjelenik egy sárga szalagcím. A következő két eset egyikéhez tartozhat:

- A fiókja már át lett telepítve, és készen áll a Fiókbeállítások felügyeletére a partner Centerben.
- A fiókja nem lett áttelepítve a partneri központba, és további műveleteket kell végrehajtania.

### <a name="your-account-has-been-migrated-to-partner-center"></a>A fiókja át lett telepítve a partner központba

Most már kezelheti a fiókját a partner Centerben. A módosításokat, például a felhasználók hozzáadását/törlését a rendszer szinkronizálja a CPP-vel.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Még nem telepítette át a fiókját a partner központba

A fiók áttelepítési folyamatának elindításához kattintson a szalagcímre. A következő elemeket kell megadnia:

1. Munkahelyi e-mail-cím: <br> <br> A legtöbb esetben jelentkezzen be a CPP-be való bejelentkezéshez használt e-mail-címmel. Bizonyos esetekben egy másik e-mail-címet kell használni:

    * Microsoft-fiók: Ha a CPP-fiók egy Microsoft-fiók, adjon meg egy érvényes munkahelyi e-mailt ahhoz a bérlőhöz, amelyhez az Microsoft Partner Network (MPN) azonosító regisztrálva van. További információ: [regisztráció Microsoft Partner Network programra](#sign-up-for-microsoft-partner-network-program).

    * Nem megfelelő bérlő: Ha a munkahelyi e-mail-címe nem tartozik a CPP-fiókjában található Microsoft Partner Network-AZONOSÍTÓhoz társított bérlőhöz, hibaüzenet jelenik meg. Ha ezt a hibát szeretné áthelyezni, adjon meg egy, a bérlőhöz társított e-mail-címet. A megjelenő hibaüzenet megadja a bérlő nevét.

2. Regisztráció Microsoft Partner Network programra

    Ha a CPP-fiók nem rendelkezik Microsoft Partner Network-AZONOSÍTÓval, vagy amely érvénytelen, akkor regisztrálnia kell a Microsoft Partner Network programra az aktiválási folyamat részeként.

## <a name="publishers-moving-from-cpp"></a>A CPP-ből áthelyezett közzétevők

Ha a fiókját áttelepítette a CPP-ből, nem kell új partner Center-fiókot létrehoznia. A meglévő CPP-fiókba való bejelentkezés után testreszabott hivatkozást kell kapnia az új partner Center-fiókhoz az e-mailben és a szalagcím-értesítésben.

Miután engedélyezte az új partner Center-fiókot a testreszabott hivatkozás meglátogatásával, visszatérhet a fiókjához, ha meglátogatja a partner Center [kereskedelmi piactér irányítópultját](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

A közzétételi szerződés és a vállalati profil adatai átkerülnek az új partner Center-fiókjába, valamint a fiókhoz korábban beállított kifizetési profilokra vonatkozó információk, felhasználói fiókok és engedélyek, valamint a CPP-fiókhoz társított aktív ajánlatok.

Miután a fiókadatok átkerülnek a CPP-ből a partneri központba, már nem fogja használni a CPP-t a fiók frissítéseinek elvégzéséhez, illetve a felhasználók, az engedélyek és a számlázás kezeléséhez. Korlátozott ideig a rendszer a fiókpartner összes olyan fiókjának frissítését automatikusan frissíti az írásvédett CPP-fiókban, amíg a CPP-portál nem elavult.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Regisztráció Microsoft Partner Network programra

A Microsofthoz csatlakozni kívánó vállalatoknak csatlakoztatnia kell a Microsoft Partner Network (MPN), és be kell szereznie egy MPN-azonosítót. Ha már tagja a Microsoft Partner Networknak, és rendelkezik MPN-AZONOSÍTÓval, akkor a fiók aktiválási folyamata során a szükséges adatok megtartásával is kéznél kell lennie.  

Ha nem tagja a Microsoft Partner Networknek, [itt csatlakozhat](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) az MPN-azonosítóhoz. Jegyezze fel az MPN-azonosítót, mert a fiók aktiválási folyamata során meg kell adnia.

További információ a Microsoft Partner Networkről: [Csatlakozás a Microsoft Partner Network](https://partner.microsoft.com/membership) a partner webhelyén. Ha többet szeretne megtudni a Microsoft Partner Network ISV-előnyeiről, tekintse meg az [ISV erőforrás-hubot](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Dynamics 365-és PowerApps-ajánlatok áthelyezése a partneri központba

A Dynamics 365 ügyfél-engagement, a PowerApps és a Dynamics 365-műveletekhez tartozó fiók-és ajánlat-kezelés egyszerűsítése érdekében a rendszer áthelyezte az ajánlatokat a [partneri központba](https://partner.microsoft.com/). Az áthelyezés biztosítja, hogy ugyanaz a tartalom elérhető legyen a nyilvános és az értékesítői katalógusokban is.

A Dynamics 365 Customer engagement, a PowerApps és a Dynamics 365 üzemeltetési ajánlatok **2019. október 15-** én elvégzendő Részletes információkért kövesse az alábbi utasításokat.

> [!NOTE]
> Ez nem vonatkozik a Dynamics 365 Business Central-ajánlatokra.  

1. Ha az MPN-tagsági fiók eredetileg a Partner tagsági központban (PMC) lett létrehozva, jelentkezzen be a [partner központba](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , és erősítse meg, hogy a fiókja át lett telepítve. Ha a profil képernyőjét az MPN-AZONOSÍTÓval látja, készen áll a folytatásra. Ha nem, el kell indítania a fiók áttelepítését a [Partner tagsági központban](https://partners.microsoft.com/partnerprogram/Welcome.aspx)megjelenő utasításokat követve. Ha segítségre van szüksége, látogasson el a [támogatási szolgálathoz](https://partner.microsoft.com/support?issueid=100-0077).
2. Lépjen a [kereskedelmi piactér – áttekintés lapra a partner Centerben](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Ha a bal oldali navigációs panelen a "kereskedelmi piactér" látható, akkor a rendszer regisztrálja, és folytatja a következő lépéssel. Ha nem, [Regisztráljon most a kereskedelmi piactéren](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) .
3. Az ajánlatait [megkeresve](https://appsource.microsoft.com/)erősítse meg ajánlatait a AppSource. Ha az ajánlatok már a AppSource-ban vannak, folytassa a következő lépéssel. A AppSource-ben nem szereplő bármely ajánlathoz hozzon létre egy [új dynamics 365 Customer engagement-ajánlatot](create-new-customer-engagement-offer.md) vagy egy [új Dynamics 365-műveleti ajánlatot](create-new-operations-offer.md).
4. A partner Center [szerződések lapján](https://partner.microsoft.com/dashboard/account/agreements)ellenőrizze, hogy áttekintette-e a **Business Applications ISV-kiegészítést**.
5. A partner Center [Fiókbeállítások](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)területen ellenőrizze, hogy a számlázási adatok készen állnak-e.
6. Az új és a meglévő ajánlatokat beküldheti minősítésre és közzétételre, még akkor is, ha az ajánlatait korábban tanúsították.
    * Töltse ki az információs képernyőket, beleértve az alkalmazás minősítéshez való biztosítását, valamint a marketing-információkat. Válassza a **Küldés** (a képernyő jobb felső sarkában) **, 2019. október 15-ig**. Ezeket a lépéseket el kell végezni az ajánlat rendelkezésre állásának elkerülése érdekében.
    * Ha jogosult, kérheti, hogy részt vegyen a prémium szintű csomagban a folyamat során.
    * A minősítés vagy az újraminősítés megköveteli, hogy az alkalmazás támogassa a Business Applications platform legújabb verzióját.
    * Az alkalmazás jóváhagyása után egy e-mailt fog kapni az ajánlathoz való visszatéréshez, és a "Go Live" lehetőség kiválasztásával engedélyezheti, hogy az ajánlat élőben Microsoft AppSource.

## <a name="additional-resources"></a>További források

A [Microsoft Dynamics CRM](https://community.dynamics.com/crm?wa=wsignin1.0)-ben segítséget kaphat a fórumok szakértőitől és szakértőitől, valamint a Blogok, a webes előadások, a videók, az események és egyebek megismerésére.

Ha segítségre van szüksége a Piactéri ajánlatok közzétételéhez, hitelesítéséhez vagy kezeléséhez, [nyújtson be egy támogatási jegyet](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-step"></a>Következő lépés

- [A kereskedelmi piactér-fiók kezelése a partner Centerben](./manage-account.md)
