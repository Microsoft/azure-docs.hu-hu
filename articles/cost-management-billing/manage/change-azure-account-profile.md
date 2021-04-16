---
title: Azure-beli számlázási fiók kapcsolattartási adatainak módosítása
description: Ez a cikk ismerteti, hogyan módosíthatók az Azure-beli számlázási fiókban megadott kapcsolattartási adatok
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/08/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: f394b6b44b2030253f7b78ec68459819c82c3c27
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480902"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>Azure-beli számlázási fiók kapcsolattartási adatainak módosítása

Ez a cikk a *számlázási fiók* esetében megadott kapcsolattartási adatoknak az Azure Portalon történő frissítéséhez nyújt segítséget. A kapcsolattartási adatok frissítésére vonatkozó utasítások a számlázási fiók típusától függően eltérnek. A számlázási fiókokkal és a saját fiókja típusával kapcsolatos további információkért lásd [a számlázási fiókoknak az Azure Portalon történő megtekintését](view-all-accounts.md) ismertető cikket. Az Azure-beli számlázási fiók nem azonos az Azure-beli felhasználói fiókkal vagy a [Microsoft-fiókkal](https://account.microsoft.com/).

Ha a frissíteni kell egy Active Directory-felhasználó profilját, annak adatait csak egy felhasználói rendszergazda módosíthatja. Ha nincs hozzárendelve a Felhasználói rendszergazda szerepkörhöz, forduljon a felhasználói rendszergazdájához. A felhasználói profilok módosítására vonatkozó további információért lásd: [Felhasználói profil adatainak hozzáadása vagy frissítése az Azure Active Directory használatával](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

*Vevő címe* – A vevő címe annak a szervezetnek vagy magánszemélynek a címe és kapcsolattartási adatai, amely vagy aki a számlázási fiókért felelős. A számlázási fiók számára létrehozott számlák mindegyikén látható.

*Számlázási cím* – A számlázási cím annak a szervezetnek vagy magánszemélynek a címe és kapcsolattartási adatai, amely vagy aki a számlázási fiók számára létrehozott számlákért felelős. A Microsoft Online Services Programhoz (MOSP) tartozó számlázási fiókok esetén egy számlázási cím van, amely a fiók számára létrehozott számlák mindegyikén látható. A Microsoft-ügyfélszerződéshez (MCA) tartozó számlázási fiókok esetén minden számlázási profilhoz tartozik egy számlázási cím, amely a számlázási profil számára létrehozott számlán látható.

*Kapcsolattartási e-mail-cím a szolgáltatási és marketingcélú e-mailekhez* – Megadhat a bejelentkezéshez használt e-mail-címtől eltérő e-mail-címet, amellyel az Azure-fiókkal kapcsolatos fontos számlázási, szolgáltatási és ajánlási értesítéseket fogadhat. A szolgáltatással kapcsolatos értesítő e-maileket, például a sürgős biztonsági problémákkal, az árváltozásokkal vagy a fiókja által használt szolgáltatások kompatibilitástörő változásaival kapcsolatos értesítéseket a rendszer mindig a bejelentkezési címére küldi.

## <a name="update-an-mosp-billing-account-address"></a>MOSP számlázási fiók címének frissítése

1. Jelentkezzen be az Azure Portalra azzal az e-mail-címmel, amely fiókadminisztrátori jogosultsággal rendelkezik a fiókra vonatkozóan.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
    ![Képernyőkép az Azure Portal keresőmezőjének helyéről, a Költségkezelés + számlázás keresőkifejezés használatával](./media/change-azure-account-profile/search-cmb.png)
1. Válassza a **Tulajdonságok** elemet a bal oldalon.  
    ![Képernyőkép az MOSP számlázási fiók tulajdonságairól](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. A vevői és a számlázási cím frissítéséhez válassza a **Számlázási cím frissítése** elemet. Adja meg az új címet, majd válassza a **Mentés** parancsot.  
    ![Képernyőkép az MOSP számlázási fiókhoz tartozó cím frissítéséről](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>MCA számlázási fiók vevői címének frissítése

1. Jelentkezzen be az Azure Portalra azzal az e-mail-címmel, amely tulajdonosi vagy közreműködői szerepkörrel rendelkezik a Microsoft-ügyfélszerződés számlázási fiókjára vonatkozóan.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
    ![Képernyőkép az Azure Portal keresőmezőjének helyéről](./media/change-azure-account-profile/search-cmb.png)
1. Válassza a **Tulajdonságok** elemet a bal oldalon, majd válassza a **Vevő címének frissítése** lehetőséget.  
    ![Képernyőkép egy MCA számlázási fiók tulajdonságairól, ahol a vevői cím módosítható](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Adja meg az új címet, majd válassza a **Mentés** parancsot.  
    ![Képernyőkép egy MCA fiók vevői címének frissítéséről](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Egyes fiókok további igazolást igényelnek, mielőtt a vevői cím frissíthető lenne. Ha a fiókja manuális jóváhagyást igényel, a rendszer megkéri, hogy forduljon az Azure ügyfélszolgálatához.

## <a name="update-an-mca-billing-account-address"></a>MCA számlázási fiók címének frissítése

1. Jelentkezzen be az Azure Portalra azzal az e-mail-címmel, amely tulajdonosi vagy közreműködői szerepkörrel rendelkezik az MCA számlázási fiókjára vagy számlázási profiljára vonatkozóan.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
1. Válassza a **Számlázási profilok** lehetőséget a bal oldalon.
1. Válasszon ki egy számlázási profilt a számlázási cím frissítéséhez.  
    ![Képernyőkép a Számlázási profilok oldalról, ahol kiválasztható egy számlázási profil](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Válassza a **Tulajdonságok** elemet a bal oldalon.
1. Válassza a **Cím frissítése** lehetőséget.  
    ![Képernyőkép a címfrissítési lehetőség helyéről](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Adja meg az új címet, majd válassza a **Mentés** parancsot.  
    ![Képernyőkép a cím frissítéséről](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="update-a-po-number"></a>Rendelésszám frissítése

Alapértelmezés szerint a számlázási profilhoz tartozó számlákhoz nincs rendelésszám társítva. Miután hozzáadta a számlázási profil rendelési számát, az megjelenik a számlázási profilhoz tartozó számlákon.

A számlázási profil rendelésszámának hozzáadásához vagy változásához kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a **Cost Management + Billing,** majd válassza a **Számlázási hatókörök lehetőséget.**
1. Válassza ki a számlázási hatókört.
1. A bal oldali menü Számlázás **menüjében válassza** a **Számlázási profilok lehetőséget.**
1. Válassza ki a megfelelő számlázási profilt.
1. A bal oldali menü Beállítások **menüjében válassza** a **Tulajdonságok lehetőséget.**
1. Válassza **a Rendelésszám frissítése lehetőséget.**
1. Adjon meg egy rendelésszámot, majd válassza a **Frissítés lehetőséget.**

## <a name="service-and-marketing-emails"></a>Szolgáltatás és marketing e-mailek

Az Azure Portal 90 naponként kéri, hogy igazolja, vagy frissítse e-mail-címét. A Microsoft e-maileket küld erre az e-mail-címre az Azure-fiókkal kapcsolatos információkkal a következőkről:

- Szolgáltatási értesítések
- Biztonsági riasztások
- Számlázási célok
- Támogatás
- Marketingkommunikáció
- Az Ön Azure-használatától függő ajánlott eljárások

Adja meg azt az e-mail-címet, amelyen a fiókjához kapcsolódó e-maileket fogadni kívánja. Az e-mail-cím megadásával Ön hozzájárul ahhoz, hogy a Microsoft e-maileket küldjön Önnek.

![Példa a kapcsolattartási adatok frissítésére való felhívásra](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>A kapcsolattartási e-mail-cím módosítása

Az alábbi módszerek egyikével módosíthatja a kapcsolattartási e-mail-címet. A kapcsolattartási e-mail-cím frissítése nem frissíti a bejelentkezéshez használt e-mail-címet.

1. Ha Ön egy MOSP-fiók fiókadminisztrátora, kövesse a [MOSP számlázási fiók címének frissítése](#update-an-mosp-billing-account-address) című szakaszban ismertetett utasításokat, és válassza a **Kapcsolattartási adatok frissítése** elemet az utolsó lépésben. Ezután adja meg az új e-mail-címet.
1. Az Azure Portalon lépjen a [Kapcsolattartási adatok](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) területre, és adja meg az új e-mail-címet. 
1. Az Azure Portalon válassza ki a monogramját vagy képét tartalmazó ikont. Majd válassza ki a helyi menüt ( **...** ). Ezután válassza a **Kapcsolattartási adataim** lehetőséget a menüből, és adja meg az új e-mail-címet.

![Példa az Azure-beli e-mail cím frissítésére](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Szolgáltatási és marketing e-mailek lemondása

Szolgáltatási és marketing e-mailek lemondása:

1. A [kérelem űrlapon](https://account.microsoft.com/profile/permissions-link-request) küldje el a kérést a profil e-mail-címének használatával. E-mailen kapni fog egy hivatkozást a beállítások frissítéséhez.
1. Kattintson a hivatkozásra a **Kommunikációs engedélyek kezelése** lap megnyitásához. Ez az oldal megjeleníti a marketingkommunikáció azon típusait, amelyek az e-mail-címen engedélyezettek. Törölje minden lemondani kívánt elem jelölését, majd válassza a **Mentés** lehetőséget.  
    ![Példa a kommunikációs engedélyek kezelési oldalára](./media/change-azure-account-profile/manage-communication-permissions.png)

Ha kikapcsolja a marketingkommunikációt, a fiókja alapján továbbra is megkapja majd a szolgáltatási értesítéseket.

## <a name="update-the-email-address-that-you-sign-in-with"></a>A bejelentkezéshez használt e-mail-cím frissítése

A fiók eléréséhez használt e-mail-címet nem frissítheti. Ha azonban rendelkezik MOSP számlázási fiókkal, regisztrálhat egy másik fiókot az új e-mail-cím használatával, és átviheti az előfizetések tulajdonjogát a másik fiókba. MCA számlázási fiók esetén megadhat a fiókra vonatkozó engedélyeket az új e-mail-címhez.

## <a name="update-your-credit-card"></a>A hitelkártya frissítése

A hitelkártya frissítésével kapcsolatban lásd az [Azure-előfizetés kifizetéséhez használt hitelkártya módosításáról](change-credit-card.md) szóló részt.

## <a name="update-your-country-or-region"></a>Az ország vagy régió frissítése

Egy meglévő fiók országának vagy régiójának a módosítása nem támogatott. Létrehozhat azonban egy új fiókot egy másik országban vagy régióban, majd az Azure ügyfélszolgálatához fordulva igényelheti az előfizetés átvitelét az új fiókba.

## <a name="change-the-subscription-name"></a>Előfizetés nevének megváltoztatása

1. Jelentkezzen be az Azure Portalra, és válassza az **Előfizetés** lehetőséget a bal oldali panelen, majd válassza ki azt az előfizetést, amelyet át kíván nevezni.
1. Válassza az **Áttekintés** lehetőséget, majd a parancssávon válassza az **Átnevezés** elemet.  
    ![Példa az Azure-előfizetés átnevezésére](./media/change-azure-account-profile/rename-sub.png)
1. A név módosítását követően kattintson a **Mentés** lehetőségre.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Számlázási fiókok megtekintése](view-all-accounts.md)
