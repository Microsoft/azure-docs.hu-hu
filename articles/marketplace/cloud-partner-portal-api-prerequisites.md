---
title: API-előfeltételek – Azure Marketplace
description: Az Cloud Partner Portal API-k használatának előfeltételei.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: f5fc77b65f6a83f4f7ca8ed8b8c9294b95307735
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107312"
---
# <a name="api-prerequisites"></a>API-előfeltételek

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a-vel, és továbbra is működnek a partner Centerben. Az áttérés kis változásokat mutat be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működőképes legyen a partneri központba való áttérés után. Csak olyan meglévő termékekhez használja a CPP API-kat, amelyek már integrálva lettek a partneri központba való áttérés előtt. az új termékeknek a partner Center beküldési API-kat kell használniuk.

A Cloud Partner Portal API-k használatához két kötelező programozott eszköz szükséges: egy egyszerű szolgáltatásnév és egy Azure Active Directory (Azure AD) hozzáférési jogkivonat.

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Egyszerű szolgáltatásnév létrehozása Azure Active Directory-bérlőben

Először létre kell hoznia egy egyszerű szolgáltatásnevet az Azure AD-bérlőben. Ez a bérlő a Cloud Partner Portalban a saját engedélyeit kapja meg. A kód a személyes hitelesítő adatok helyett a Bérlővel hívja meg az API-kat. Az egyszerű szolgáltatásnév létrehozásával kapcsolatos teljes körű magyarázatért lásd [: útmutató: a portál használata az erőforrásokhoz hozzáférő Azure ad-alkalmazás és egyszerű szolgáltatásnév létrehozásához](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="add-service-principal-to-your-account"></a>Egyszerű szolgáltatásnév hozzáadása a fiókhoz

Most, hogy létrehozta a szolgáltatásnevet a bérlőben, hozzáadhatja azt felhasználóként a partner Center portál fiókjához. A felhasználóhoz hasonlóan az egyszerű szolgáltatásnév a portál tulajdonosa vagy közreműködői is lehet. Részletekért lásd alább a **következő lépéseket** .

## <a name="next-steps"></a>Következő lépések

Lásd: [Azure ad-alkalmazások kezelése](manage-aad-apps.md).
