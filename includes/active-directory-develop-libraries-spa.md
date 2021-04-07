---
ms.openlocfilehash: a393cf978318c39081805cae7e38c3386ff156f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010701"
---
| Nyelv/keretrendszer | Projekt bekapcsolva<br/>GitHub                                                                                                    | Csomag                                                                      | Szerzés<br/>lépések                             | Bejelentkezési felhasználók                                         | Hozzáférés a webes API-khoz                                                 | Általánosan elérhető (GA) *vagy*<br/>Nyilvános előzetes verzió<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup> . szögletes         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
| Angular              | <sup>3</sup> . [szögletes MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Oktatóanyag](../articles/active-directory/develop/tutorial-v2-angular.md)| ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
|JavaScript|[MSAL.js 1,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Oktatóanyag](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | FE                                                           |
| React                | [MSAL reagál](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![A könyvtár a felhasználói bejelentkezéshez igényelhet azonosító jogkivonatokat.][y] | ![A könyvtár hozzáférési jogkivonatokat kérhet a védett webes API-khoz.][y] | Nyilvános előzetes verzió                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> a Microsoft Azure előzetes verziókra [vonatkozó kiegészítő használati feltételek][preview-tos] a *nyilvános előzetes* verzióban lévő tárakra vonatkoznak.

<sup>2</sup> [hitelesítési kód][auth-code-flow] csak PCKE-mel (ajánlott). 

<sup>3</sup> [implicit engedélyezési folyamat][implicit-flow] .

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md