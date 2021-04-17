---
ms.openlocfilehash: 05fc91667f34262c6b510c0e1464d3e5fad339bc
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107590114"
---
| Nyelv/keretrendszer | Projekt a<br/>GitHub                                                                                                    | Csomag                                                                      | Szerzés<br/>Kezdett                             | Felhasználók bejelentkezése                                         | Webes API-k elérése                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes<sup>verzió 1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Oktatóanyag](../articles/active-directory/develop/tutorial-v2-angular.md)| ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
|JavaScript|[MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![A kódtár azonosító jogkivonatokat kérhet a felhasználói bejelentkezéshez.][y] | ![A kódtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Az előzetes verziók kiegészítő használati Microsoft Azure a][preview-tos] nyilvános előzetes verzióban elérhető *kódtárakra vonatkoznak.*

<sup>2A</sup> [hitelesítési kódfolyam csak][auth-code-flow] PCKE-vel (ajánlott). 

<sup>3 Csak</sup> [implicit engedélyfolyam.][implicit-flow]

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md