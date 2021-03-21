---
title: A Azure Data Catalog hibáinak megoldása
description: Ez a cikk a Azure Data Catalog erőforrásaival kapcsolatos gyakori hibaelhárítási problémákat ismerteti.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 201a7d1fce00323ef3f8eec1bdd415c4064d49ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674988"
---
# <a name="troubleshooting-azure-data-catalog"></a>Az Azure Data Catalog hibaelhárítása

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Ez a cikk a Azure Data Catalog erőforrásaival kapcsolatos gyakori hibaelhárítási problémákat ismerteti. 

## <a name="functionality-limitations"></a>Működési korlátozások

Azure Data Catalog használatakor a következő funkciók korlátozottak:

- A **vendég szerepkörrel** rendelkező fiókok nem támogatottak. Azure Data Catalog felhasználóinak nem adhat hozzá vendég fiókokat, és a vendég felhasználók nem használhatják a portált a következő címen: [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) .

- Azure Data Catalog-erőforrások létrehozása Azure Resource Manager sablonokkal vagy Azure PowerShell parancsok használatával nem támogatott.

- A Azure Data Catalog erőforrás nem helyezhető át az Azure-bérlők között.

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory-szabályzat konfigurálása

Előfordulhat, hogy ugyan be tud lépni az Azure Data Catalog portálra, de amikor megpróbál bejelentkezni az adatforrás-regisztrációs eszközbe, hibaüzenet jelenik meg, amely meggátolja a belépést. Ez a hiba akkor fordulhat elő, ha a vállalati hálózaton van, vagy ha a vállalati hálózaton kívülről csatlakozik.

A regisztrációs eszköz *űrlapos hitelesítés* segítségével veti össze a felhasználói bejelentkezéseket az Azure Active Directory adatbázisával. Ahhoz, hogy a bejelentkezés sikeres legyen, az Azure Active Directory rendszergazdájának engedélyeznie kell az űrlapos hitelesítést a *globális hitelesítési szabályzatban*.

Ahogy az az alábbi képen is látható, a globális hitelesítési szabályzat segítségével különböző hitelesítést engedélyezhet az intranetes és az extranetes kapcsolatokhoz. Bejelentkezési hibák akkor fordulhatnak elő, ha nincs engedélyezve az űrlapos hitelesítés azon a hálózaton, amelyhez csatlakozik.

 ![Az Azure Active Directory globális hitelesítési szabályzata](./media/troubleshoot-policy-configuration/global-auth-policy.png)

További információkért lásd a [Hitelesítési házirendek konfigurálása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)) című cikket.

## <a name="next-steps"></a>Következő lépések

* [Azure Data Catalog létrehozása](data-catalog-get-started.md)
