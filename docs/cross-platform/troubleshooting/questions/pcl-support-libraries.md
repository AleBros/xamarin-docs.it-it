---
title: Come è possibile visualizzare le librerie supportate in una libreria di classi portabile (PCL)?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: conceptdev
ms.author: crdun
ms.date: 07/27/2018
ms.openlocfilehash: 31dc5114a04deaf1a35bbd24f71cfa552f61d226
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290976"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>Come è possibile visualizzare le librerie supportate in una libreria di classi portabile (PCL)?

- È possibile trovare una panoramica delle varie funzionalità supportate dalle varie piattaforme di destinazione PCL nella parte relativa alle *funzionalità supportate* di questa pagina:[https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)

- Un'altra opzione consiste nell'usare [.NET Portability Analyzer](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) per valutare se la libreria esistente può essere convertita in un profilo PCL.

- Una terza possibilità consiste nell'esplorare il contenuto del profilo effettivo che è possibile utilizzare. Con profile 78, ad esempio, è possibile fare clic qui: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\`E visualizzare tutti gli assembly al suo interno.

Indipendentemente dal metodo scelto, si noti che alcune funzionalità devono essere scaricate tramite NuGet e la libreria Microsoft BCL.
