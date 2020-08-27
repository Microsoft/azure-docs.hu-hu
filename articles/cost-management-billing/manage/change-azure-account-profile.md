---
title: Az Azure-fiókban megadott kapcsolattartási adatok módosítsa
description: Ez a cikk ismerteti, hogyan módosíthatók az Azure adminisztratív fiókban megadott kapcsolattartási adatok.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/03/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6045afba230fa204dd5f93adc11b67ff0e3e209f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684898"
---
# <a name="change-the-contact-information-for-your-azure-account"></a>Az Azure-fiókban megadott kapcsolattartási adatok módosítsa

Ez a cikk a fiókban megadott kapcsolattartási adatoknak az Azure Portalon történő frissítéséhez nyújt segítséget. A kapcsolattartási adatok frissítésére vonatkozó utasítások a számlázási fiók típusától függően eltérnek. A számlázási fiókokkal és a saját fiókja típusával kapcsolatos további információkért lásd [a számlázási fiókoknak az Azure Portalon történő megtekintését](view-all-accounts.md) ismertető cikket.

*Vevő címe* – A vevő címe annak a szervezetnek vagy magánszemélynek a címe és kapcsolattartási adatai, amely vagy aki a számlázási fiókért felelős. A számlázási fiók számára létrehozott számlák mindegyikén látható.

*Számlázási cím* – A számlázási cím annak a szervezetnek vagy magánszemélynek a címe és kapcsolattartási adatai, amely vagy aki a számlázási fiók számára létrehozott számlákért felelős. A Microsoft Online Services Programhoz (MOSP) tartozó számlázási fiókok esetén egy számlázási cím van, amely a fiók számára létrehozott számlák mindegyikén látható. A Microsoft-ügyfélszerződéshez (MCA) tartozó számlázási fiókok esetén minden számlázási profilhoz tartozik egy számlázási cím, amely a számlázási profil számára létrehozott számlán látható.

*Kapcsolattartási e-mail-cím a szolgáltatási és marketingcélú e-mailekhez* – Megadhat a bejelentkezéshez használt e-mail-címtől eltérő e-mail-címet, amellyel az Azure-fiókkal kapcsolatos fontos számlázási, szolgáltatási és ajánlási értesítéseket fogadhat. A szolgáltatással kapcsolatos értesítő e-maileket, például a sürgős biztonsági problémákkal, az árváltozásokkal vagy a fiókja által használt szolgáltatások kompatibilitástörő változásaival kapcsolatos értesítéseket a rendszer mindig a bejelentkezési címére küldi.

## <a name="update-an-mosp-billing-account-address"></a>MOSP számlázási fiók címének frissítése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) azzal az e-mail-címmel, amely fiókadminisztrátori jogosultsággal rendelkezik a fiókra vonatkozóan.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/change-azure-account-profile/search-cmb.png)
1. Válassza a **Tulajdonságok** elemet a bal oldalon.  
    ![Képernyőkép a címfrissítési oldalról](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. A vevői és a számlázási cím frissítéséhez válassza a **Számlázási cím frissítése** elemet. Adja meg az új címet, majd válassza a **Mentés** parancsot.  
    ![Képernyőkép a címfrissítési oldalról](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>MCA számlázási fiók vevői címének frissítése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) azzal az e-mail-címmel, amely tulajdonosi vagy közreműködői szerepkörrel rendelkezik a Microsoft-ügyfélszerződés számlázási fiókjára vonatkozóan.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/change-azure-account-profile/search-cmb.png)
1. Válassza a **Tulajdonságok** elemet a bal oldalon, majd válassza a **Vevő címének frissítése** lehetőséget.  
    ![Képernyőkép a vevői cím frissítése szolgáló elem kiválasztásáról](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Adja meg az új címet, majd válassza a **Mentés** parancsot.  
    ![Képernyőkép a cím frissítéséről](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Egyes fiókok további igazolást igényelnek, mielőtt a vevői cím frissíthető lenne. Ha a fiókja manuális jóváhagyást igényel, a rendszer megkéri, hogy forduljon az Azure ügyfélszolgálatához.

## <a name="update-an-mca-billing-account-address"></a>MCA számlázási fiók címének frissítése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) azzal az e-mail-címmel, amely tulajdonosi vagy közreműködői szerepkörrel rendelkezik az MCA számlázási fiókjára vagy számlázási profiljára vonatkozóan.
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.  
    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/change-azure-account-profile/search-cmb.png)
1. Válassza a **Számlázási profilok** lehetőséget a bal oldalon.
1. Válasszon ki egy számlázási profilt a számlázási cím frissítéséhez.  
    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Válassza a **Tulajdonságok** elemet a bal oldalon.
1. Válassza a **Cím frissítése** lehetőséget.  
    ![Képernyőkép a „költségkezelés + számlázás” kifejezés portálon történő kereséséről](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Adja meg az új címet, majd válassza a **Mentés** parancsot.  
    ![Képernyőkép a cím frissítéséről](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>Szolgáltatás és marketing e-mailek

Az [Azure Portal](https://portal.azure.com) 90 naponként kéri, hogy igazolja, vagy frissítse e-mail-címét. A Microsoft e-maileket küld erre az e-mail-címre az Azure-fiókkal kapcsolatos információkkal a következőkről:

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

* Ha Ön egy MOSP-fiók fiókadminisztrátora, kövesse a [MOSP számlázási fiók címének frissítése](#update-an-mosp-billing-account-address) című szakaszban ismertetett utasításokat, és válassza a **Kapcsolattartási adatok frissítése** elemet az utolsó lépésben. Ezután adja meg az új e-mail-címet.

* Az Azure Portalon lépjen a [Kapcsolattartási adatok](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) területre, és adja meg az új e-mail-címet. 

* Az [Azure Portalon](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) válassza ki a monogramját vagy képét tartalmazó ikont. Majd válassza ki a helyi menüt ( **...** ). Ezután válassza a **Kapcsolattartási adataim** lehetőséget a menüből, és adja meg az új e-mail-címet.

![Példa az Azure-beli e-mail cím frissítésére](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Szolgáltatási és marketing e-mailek lemondása

Szolgáltatási és marketing e-mailek lemondása:

1. A [kérelem űrlapon](https://account.microsoft.com/profile/permissions-link-request) küldje el a kérést a profil e-mail-címének használatával. E-mailen kapni fog egy hivatkozást a beállítások frissítéséhez.
1. Kattintson a hivatkozásra a **Kommunikációs engedélyek kezelése** lap megnyitásához. Ez az oldal megjeleníti a marketingkommunikáció azon típusait, amelyek az e-mail-címen engedélyezettek. Törölje minden lemondani kívánt elem jelölését, majd válassza a **Mentés** lehetőséget.  
    ![Példa a kommunikációs engedélyek kezelési oldalára](./media/change-azure-account-profile/manage-communication-permissions.png)

Ha kikapcsolja a marketingkommunikációt, a fiókja alapján továbbra is megkapja majd a szolgáltatási értesítéseket.

## <a name="update-the-email-address-that-you-sign-in-with"></a>A bejelentkezéshez használt e-mail-cím frissítése

A fiók eléréséhez használt e-mail-címet nem frissítheti. Ha azonban rendelkezik MOSP számlázási fiókkal, regisztrálhat egy másik fiókot az új e-mail-cím használatával, és átviheti az előfizetések tulajdonjogát a másik fiókba. MCA számlázási fiók esetén [megadhat a fiókra vonatkozó engedélyeket az új e-mail-címhez](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="update-your-credit-card"></a>A hitelkártya frissítése

A hitelkártya frissítésével kapcsolatban lásd az [Azure-előfizetés kifizetéséhez használt hitelkártya módosításáról](change-credit-card.md) szóló részt.

## <a name="update-your-country-or-region"></a>Az ország vagy régió frissítése

Egy meglévő fiók országának vagy régiójának a módosítása nem támogatott. Létrehozhat azonban egy új fiókot egy másik országban vagy régióban, majd az Azure ügyfélszolgálatához fordulva igényelheti az előfizetés átvitelét az új fiókba.

## <a name="change-the-subscription-name"></a>Előfizetés nevének megváltoztatása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Előfizetés** lehetőséget a bal oldali panelen, majd válassza ki azt az előfizetést, amelyet át kíván nevezni.
1. Válassza az **Áttekintés** lehetőséget, majd a parancssávon válassza az **Átnevezés** elemet.  
    ![Példa az Azure-előfizetés átnevezésére](./media/change-azure-account-profile/rename-sub.png)
1. A név módosítását követően kattintson a **Mentés** lehetőségre.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Számlázási fiókok megtekintése](view-all-accounts.md)
