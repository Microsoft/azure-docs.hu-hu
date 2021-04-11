---
title: Az elemzési adatszolgáltatások programozott elérésének előfeltételei
description: Megtudhatja, hogy milyen követelmények szükségesek ahhoz, hogy programozott módon hozzáférhessen a kereskedelmi Piactéri elemzési adathoz.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 1cdd3dba8203ce9e8daeaa963f1722389d89d19d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563820"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>Az elemzési adatszolgáltatások programozott elérésének előfeltételei

Ahhoz, hogy programozott módon hozzáférhessen a kereskedelmi piactér elemzési adatszolgáltatásához, meg kell felelnie az alábbi követelményeknek.

## <a name="commercial-marketplace-enrollment"></a>Kereskedelmi piactér-regisztráció

A kereskedelmi Piactéri elemzési adatszolgáltatások programozott módon való eléréséhez regisztrálni kell a kereskedelmi piactér programban, és rendelkeznie kell egy partner Center-fiókkal. Ebből a cikkből megtudhatja, hogyan [hozhat létre kereskedelmi Piactéri fiókot a partner Centerben](./partner-center-portal/create-account.md).

## <a name="create-azure-active-directory-application"></a>Azure Active Directory alkalmazás létrehozása

A rendszeres felhasználói hitelesítő adatok nem használhatók a kereskedelmi piactér elemzési adatainak programozott eléréséhez. Létre kell hozni egy Azure Active Directory (Azure AD) alkalmazást az elemzési API-k eléréséhez szükséges titkos kulccsal együtt. Az Azure AD-alkalmazások és-titkos kódok létrehozásával kapcsolatos információkért lásd a gyors útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](../active-directory/develop/quickstart-register-app.md)című témakört.

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>Az Azure AD-alkalmazás hozzárendelése a partner Center-bérlőhöz

A Azure Portalban létrehozott Azure AD-alkalmazást össze kell kapcsolni a partner Center-fiókkal. Ennek lépései a következők:

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard).
1. A jobb felső sarokban válassza a fogaskerék ikont, majd válassza a **Fiókbeállítások** lehetőséget.
1. A **Fiókbeállítások** menüben válassza a **felhasználói kezelés** lehetőséget.
1. Válassza az **Azure ad-alkalmazások** lehetőséget, majd válassza az **+ Azure ad-alkalmazás létrehozása** lehetőséget.
1. Válassza ki a Azure Portal létrehozott Azure AD-alkalmazást, majd kattintson a **tovább** gombra.
1. Jelölje be a **kezelő (Windows)** jelölőnégyzetet, majd kattintson a **Hozzáadás** gombra.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="Bemutatja az Azure AD-alkalmazás létrehozása lapot a szerepkörök kiválasztására szolgáló jelölőnégyzetekkel.":::

## <a name="generate-an-azure-ad-token"></a>Azure AD-jogkivonat létrehozása

Az alkalmazás (ügyfél) azonosítójával kell létrehoznia egy Azure AD-jogkivonatot. Ez az azonosító segítséget nyújt az ügyfélalkalmazás egyedi azonosításához a Microsoft Identity platformon, valamint az előző lépésben az ügyfél titkos kulcsát. Az Azure AD-token létrehozásának lépéseiért lásd: [szolgáltatások közötti hívások ügyfél-hitelesítő adatok használatával (közös titok vagy tanúsítvány)](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).

> [!NOTE]
> A jogkivonat egy órára érvényes.

## <a name="next-steps"></a>Következő lépések

- [Programozott hozzáférés paradigma](analytics-programmatic-access.md)