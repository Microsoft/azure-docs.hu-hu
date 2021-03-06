---
title: Az Azure-regisztráció során elutasított bankkártyával kapcsolatos probléma elhárítása
description: Az Azure Portalon vagy a Fiókközpontban az Azure-regisztráció során elutasított hitelkártyával kapcsolatos probléma megoldása.
author: v-miegge
ms.reviewer: judupont
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5b53d1855abb3114be1ab31e19af3bb3a11d722f
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132159"
---
# <a name="troubleshoot-a-declined-card-at-azure-sign-up"></a>Az Azure-regisztráció során elutasított bankkártyával kapcsolatos probléma elhárítása

Előfordulhat, hogy olyan problémát vagy hibát tapasztal, amikor a rendszer elutasít egy hitelkártyát az Azure-regisztráció során a Microsoft Azure Portalon vagy az Azure Fiókközpontban.

A probléma megoldásához válassza ki az alábbi témák közül azt, amelyik leginkább hasonlít a hibára.

## <a name="the-credit-card-provider-is-not-accepted-for-your-countryregion"></a>Nem elfogadott a hitelkártya-kibocsátó az adott ország vagy régió esetében.

A kártya kiválasztásakor az Azure megjeleníti a választott országban vagy régióban elfogadott kártyatípusokat. Lépjen kapcsolatba bankjával vagy a kártya kibocsátójával, és ellenőrizze, hogy a hitelkártya esetében engedélyezettek-e a nemzetközi tranzakciók. További információ a támogatott országokról vagy régiókról és pénznemekről: [Azure-vásárlással kapcsolatos gyakori kérdések](https://azure.microsoft.com/pricing/faq/).

>[!Note]
>Az American Express hitelkártyák jelenleg nem minősülnek elfogadott fizetőeszköznek Indiában. Nem áll módunkban tudni, mikor válnak elfogadott fizetőeszközzé.

## <a name="youre-using-a-virtual-or-prepaid-card"></a>Virtuális kártya vagy feltöltőkártya használata

A virtuális hitelkártyák vagy bankkártyák és a feltöltőkártyák nem használhatók fizetőeszközként Azure-előfizetésekhez.

## <a name="your-credit-information-is-inaccurate-or-incomplete"></a>Pontatlan vagy hiányos hitelkártyaadatok

A névnek, címnek és a CVV-kódnak pontosan meg kell egyeznie a kártyán szereplő adatokkal.

## <a name="the-card-is-inactive-or-blocked"></a>A kártya nincs aktiválva vagy le van tiltva

Lépjen kapcsolatba a bankkal, és ellenőrizze, hogy a kártyája aktiválva van-e.

Egyéb problémák is jelentkezhetnek a regisztrációval kapcsolatban.

További információt az Azure-ba történő regisztráció során adódó problémák hibaelhárításával kapcsolatban a következő tudásbázisbeli cikkben talál:

[Nem tud regisztrálni az Azure-ba az Azure Portalon vagy az Azure Fiókközpontban](troubleshoot-azure-sign-up.md)

## <a name="you-represent-a-business-that-doesnt-want-to-pay-by-card"></a>Olyan vállalkozást képvisel, amely nem kíván kártyával fizetni

Egy vállalkozás képviselőjeként más fizetési módok használatával, például csekken és banki átutalással is kiegyenlítheti az Azure-előfizetés díját. Ha a számlás fizetést választotta a fiók létrehozásakor, később nem válthat másik fizetési módra, kivéve, ha rendelkezik Microsoft Ügyfélszerződéssel, és az Azure webhelyén regisztrált az Azure-ba.

További információ a számlás fizetésről: [Az Azure-előfizetés számlás kifizetésére vonatkozó kérés elküldése](pay-by-invoice.md).

## <a name="your-credit-card-information-is-outdated"></a>A hitelkártyaadatok lejártak

További információ a kártyaadatok kezelésével kapcsolatban, beleértve a kártyák módosítását vagy eltávolítását: [Hitelkártya hozzáadása, frissítése vagy eltávolítása az Azure-ban](change-credit-card.md).

## <a name="additional-help-resources"></a>További támogatási forrásanyagok

Az Azure-számlázással és -előfizetésekkel kapcsolatos további hibaelhárítási cikkek

- [Regisztrálással kapcsolatos problémák](troubleshoot-azure-sign-up.md)
- [Bejelentkezéssel kapcsolatos problémák az előfizetésben](troubleshoot-sign-in-issue.md)
- [Nem található előfizetés](no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Ha segítségre van szüksége, vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Az Azure-számlázás dokumentációja](../index.yml)