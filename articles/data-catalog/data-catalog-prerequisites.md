---
title: Az Azure Data Catalog előfeltételei
description: További információ az Azure Data Catalog használatának megkezdéséhez szüksége előfeltételeiről.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 788e7a453e9879d9c901448659ff1c8cfa7201ff
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548310"
---
# <a name="azure-data-catalog-prerequisites"></a>Az Azure Data Catalog előfeltételei

Azure Data Catalog beállítása előtt néhány dolgot irányuló szüksége. Ne aggódjon, ez a folyamat nem vesz hosszú.

## <a name="azure-subscription"></a>Azure-előfizetés
A Data Catalog beállításához az Azure-előfizetés tulajdonosa vagy társtulajdonosa kell lennie.

Azure-előfizetés segít rendszerezni a felhőszolgáltatás erőforrások, például a Data Catalog elérését. Előfizetések segít erőforrás-használat jelentett, a számlázás és a fizetős is. Az egyes előfizetésekhez külön számlázással és fizetéssel telepítő, van így is tartozhat, előfizetések és a terveket, amely régiónként eltérő részleg, projekt, területi képviseletenként stb. Egy előfizetéshez tartozik minden olyan felhőszolgáltatáshoz, és a egy előfizetéshez, mielőtt a Data Catalog beállításához rendelkeznie kell. További információkat a [fiókok, előfizetések és rendszergazdai szerepkörök kezeléséről](../active-directory/users-groups-roles/directory-assign-admin-roles.md) szóló cikkben talál.

## <a name="azure-active-directory"></a>Azure Active Directory
A Data Catalog beállításához kell bejelentkeznie az Azure Active Directory (Azure AD) felhasználói fiókkal jelentkezik be.

Az Azure AD egyszerű módot kínál vállalkozásának az identitás és a hozzáférés kezelésére, mind a felhőben, mind a helyszínen. Felhasználók egyetlen munkahelyi vagy iskolai fiók egyetlen bejelentkezhet bármely felhőben és a helyileg üzemeltetett webes alkalmazás. A Data Catalog az Azure AD használatával hitelesíti a bejelentkező felhasználókat. További tudnivalókért lásd: [Mi az Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

> [!NOTE]
> Használatával a [az Azure portal](https://portal.azure.com/), jelentkezhet be személyes Microsoft-fiókkal vagy egy Azure Active Directory munkahelyi vagy iskolai fiókjával. Az Azure portal használatával, az a Data Catalog beállításához, vagy a [Data Catalog-portál](https://www.azuredatacatalog.com), az Azure Active Directory-fiókkal, nem a személyes fiókjával kell bejelentkeznie.
>
>

## <a name="active-directory-policy-configuration"></a>Az Active Directory-szabályzat konfigurálása
Előfordulhat olyan helyzet állhat elő, ha tud bejelentkezni a Data Catalog-portál, de amikor megpróbál bejelentkezni az adatforrás-regisztráló eszköz, találkozik egy hibaüzenet, amely megakadályozza, hogy jelentkezik be. Ez a viselkedés a probléma akkor fordulhat elő, csak akkor lehetséges, ha Ön a vállalati hálózaton, vagy akkor fordulhat elő, csak akkor, ha a vállalati hálózaton kívülről érkező csatlakoztatja.

Az adatforrás-regisztráló eszköz űrlapos hitelesítés érvényesítése az Active Directorybeli felhasználói hitelesítő adatait használja. Segítséget a bejelentkezés sikeres, Active Directory-rendszergazda engedélyeznie kell a globális hitelesítési házirend az űrlapos hitelesítés.

A globális hitelesítési házirend hitelesítési módszerek engedélyezhető külön-külön az intranetes és extranetes kapcsolatokhoz, az alábbi képernyőképen látható módon. Bejelentkezési hiba is felléphet, ha nincs engedélyezve az űrlapos hitelesítés, amelyről csatlakozik a hálózathoz.

 ![Az Active Directory globális hitelesítési házirend](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>További lépések
További információkért lásd: [hitelesítési házirendek konfigurálása](https://technet.microsoft.com/library/dn486781.aspx).
