---
title: È possibile creare un archivio xcarchive da Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 952777b3178b82657c8f64d5882c532c188c7a79
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422022"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>È possibile creare un archivio xcarchive da Visual Studio?

## <a name="for-xamarin-4"></a>Per Xamarin 4

A partire da Xamarin 4.x, è ora possibile creare un `.xcarchive` da Windows, impostare il `ArchiveOnBuild` proprietà `true`. Ad esempio, usando `MSBuild` nella riga di comando:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

Il `.xcarchive` sarà inserito nel `$HOME/Library/Developer/Xcode/Archives` directory nell'host di compilazione Mac che eseguono ricerche in Xcode e Xamarin Studio per gli archivi di visualizzazione compilato in precedenza.

Vedere questo [post di forum di Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) per alcune brevi note aggiuntive sul `ArchiveOnBuild` proprietà. Vedere la documentazione relativa [compilazioni da riga di comando di xamarin. IOS in Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per altri dettagli sulle `ServerAddress` e `ServerUser` proprietà.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Per Xamarin 3 e versioni precedenti

L'estensione di Visual Studio 3.x Xamarin non fornisce un meccanismo per produrre `.xcarchive` archivi. Ciò premesso, la logica usata per creare `.xcarchive` gli archivi di Xamarin Studio in Mac [descritto di seguito](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), quindi è probabilmente è stato possibile creare una propria `.xcarchive` manualmente se si vuole.

Ma vale la pena notare che non è necessario un `.xcarchive` inviare per l'App Store. È possibile inviare un file IPA, purché sia firmato con un profilo di distribuzione di App Store (non un profilo distribuzione Ad Hoc).

In effetti, è possibile anche solo comprimerà i `.app` bundle (che viene firmato con un profilo di distribuzione App Store) e inviare tale `.zip` file nell'app Store.

In entrambi i casi, è possibile usare l'app Application Loader per inviare l'app (anziché Xcode).

