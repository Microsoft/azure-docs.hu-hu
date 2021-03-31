---
title: B2B együttműködés felhasználói jogcímek leképezése – Azure Active Directory
description: Szabja testre az SAML-jogkivonatban kiállított felhasználói jogcímeket Azure Active Directory (Azure AD) B2B-felhasználók számára.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87909670"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>VÁLLALATKÖZI együttműködés felhasználói jogcímek leképezése Azure Active Directory

Azure Active Directory (Azure AD) támogatja az SAML-jogkivonatban kiadott jogcímek testreszabását a B2B együttműködési felhasználók számára. Amikor egy felhasználó hitelesíti az alkalmazást, az Azure AD egy SAML-jogkivonatot bocsát ki az alkalmazásnak, amely az egyedileg azonosított felhasználóval kapcsolatos információkat (vagy jogcímeket) tartalmaz. Alapértelmezés szerint ez magában foglalja a felhasználó felhasználónevét, e-mail-címét, utónevét és vezetéknevét.

A [Azure Portal](https://portal.azure.com)megtekintheti vagy szerkesztheti az SAML-jogkivonatban eljuttatott jogcímeket az alkalmazásnak. A beállítások eléréséhez válassza **Azure Active Directory**  >  **vállalati alkalmazások** lehetőséget > az egyszeri bejelentkezéshez > **egyszeri bejelentkezéshez** konfigurált alkalmazást. Tekintse meg az SAML-jogkivonat beállításait a **felhasználói attribútumok** szakaszban.

![Megjeleníti az SAML-jogkivonat attribútumait a felhasználói felületen](media/claims-mapping/view-claims-in-saml-token.png)

Az SAML-jogkivonatban kiadott jogcímek szerkesztésének két lehetséges oka lehet:

1. Az alkalmazáshoz különböző jogcím-URI-k vagy jogcím-értékek szükségesek.

2. Az alkalmazás megköveteli, hogy a NameIdentifier jogcím az Azure AD-ben tárolt egyszerű felhasználónév (UPN) helyett legyen.

A jogcímek hozzáadásával és szerkesztésével kapcsolatos további információkért lásd: [az SAML-jogkivonatban kiadott jogcímek testreszabása a Azure Active Directory-ben nagyvállalati alkalmazásokhoz](../develop/active-directory-saml-claims-customization.md).

A B2B-együttműködés felhasználói számára a NameID és az UPN-t használó több-bérlős felhasználók biztonsági okokból nem érhetők el.

## <a name="next-steps"></a>Következő lépések

- További információ a B2B együttműködés felhasználói tulajdonságairól: [Azure Active Directory B2B csoportmunka-felhasználó tulajdonságai](user-properties.md).
- A B2B-együttműködés felhasználói jogkivonatával kapcsolatos információkért lásd: [felhasználói jogkivonatok megismerése az Azure ad B2B együttműködésben](user-token.md).

