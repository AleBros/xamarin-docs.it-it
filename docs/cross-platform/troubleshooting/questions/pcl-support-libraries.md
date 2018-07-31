---
title: Come è possibile visualizzare le librerie supportate in una libreria di classi Portabile?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: asb3993
ms.author: amburns
ms.date: 07/27/2018
ms.openlocfilehash: 7e1017baf7daed68b5e55319a9ce13a4a2df5f2e
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351470"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>Come è possibile visualizzare le librerie supportate in una libreria di classi Portabile?

- È possibile trovare una panoramica delle diverse funzionalità supportate per le varie piattaforme di destinazione di libreria di classi Portabile nel *funzionalità supportate* parte di questa pagina: [https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- Un'altra opzione consiste nell'usare la [.NET Portability Analyzer](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) per valutare se la libreria esistente può essere convertita in un profilo di libreria di classi Portabile.

- Una terza possibilità consiste nell'esplorare i contenuti del profilo effettivo che è possibile utilizzare. Utilizza profilo 78, ad esempio, potrebbe vedere qui: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` e visualizzare tutti gli assembly all'interno di esso.

Indipendentemente dal metodo scelto,. si noti che alcune funzionalità deve essere scaricato tramite NuGet e la libreria Microsoft BCL.
