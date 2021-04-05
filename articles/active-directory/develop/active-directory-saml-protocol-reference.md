---
title: Hogyan használja a Microsoft Identity platform az SAML protokollt?
description: Ez a cikk áttekintést nyújt a Azure Active Directory egyetlen Sign-On és egy Sign-Out SAML-profiljairól.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 4e9d63b8fe7fc281a87deb27ddadd794e14fa04d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755623"
---
# <a name="how-the-microsoft-identity-platform-uses-the-saml-protocol"></a>Hogyan használja a Microsoft Identity platform az SAML protokollt?

A Microsoft Identity platform az SAML 2,0 protokollt használja, amellyel lehetővé teheti az alkalmazások számára, hogy egyszeri bejelentkezéses felhasználói élményt nyújtsanak a felhasználóknak. Az Azure AD egyszeri bejelentkezési és [egyszeri kijelentkezési](single-sign-out-saml-protocol.md) SAML-profiljai elmagyarázzák, hogyan használják az SAML- [jogcímeket](single-sign-on-saml-protocol.md) , protokollokat és kötéseket az Identity Provider szolgáltatásban.

Az SAML protokoll megköveteli az Identitáskezelő (Microsoft Identity platform) és a szolgáltató (az alkalmazás) számára a saját magukkal kapcsolatos információk cseréjét.

Ha egy alkalmazás regisztrálva van az Azure AD-ben, az alkalmazás fejlesztője regisztrálja az összevonási szolgáltatással kapcsolatos információkat az Azure AD-vel. Ez az információ magában foglalja az alkalmazás **átirányítási URI** -ját és **metaadatait** .

A Microsoft Identity platform a Cloud Service metaadat- **URI-ját** használja az aláíró kulcs és a kijelentkezési URI lekéréséhez. Az ügyfél megnyithatja az alkalmazást az **Azure ad-> alkalmazás-regisztrációban** , majd a **Beállítások-> tulajdonságaiban** a kijelentkezési URL-címet is frissítheti. Így a Microsoft Identity platform elküldheti a választ a megfelelő URL-címre. 

Azure Active Directory a bérlő-specifikus és a közös (bérlői független) egyszeri bejelentkezéses és egyszeri kijelentkezési végpontokat teszi elérhetővé. Ezek az URL-címek címezhető helyet jelölnek – nem csupán azonosítók –, így a végpontra léphet a metaadatok olvasásához.

* A bérlő-specifikus végpont a következő helyen található: `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` . A *\<TenantDomainName>* helyőrző egy Azure ad-bérlő regisztrált tartománynevét vagy TenantID GUID azonosítóját jelöli. Az contoso.com-bérlő összevonási metaadatai például a következő helyen találhatóak: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

* A bérlőtől független végpont a következő helyen található: `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml` . Ebben a végpontban az **általános** név a bérlői tartománynév vagy azonosító helyett jelenik meg.

Az Azure AD által közzétett összevonási metaadatokat tartalmazó dokumentumokkal kapcsolatos információkért lásd: [összevonási metaadatok](../azuread-dev/azure-ad-federation-metadata.md).
