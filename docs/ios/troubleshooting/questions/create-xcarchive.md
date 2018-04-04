---
title: È possibile creare un archivio .xcarchive da Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 23af3bf277ab68ffe93df2e1ee8ee64afc01828d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>È possibile creare un archivio .xcarchive da Visual Studio?

## <a name="for-xamarin-4"></a>Per Xamarin 4

A partire da Xamarin 4. x, è ora possibile creare un `.xcarchive` da Windows mediante l'impostazione di `ArchiveOnBuild` proprietà `true`. Ad esempio, usando `MSBuild` nella riga di comando:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

Il `.xcarchive` verrà inserita nel `$HOME/Library/Developer/Xcode/Archives` directory nell'host di compilazione Mac che eseguono ricerche in Xcode e Xamarin Studio per gli archivi di visualizzazione creati in precedenza.

Vedere questo [post dei forum di Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) per alcune brevi note aggiuntive sul `ArchiveOnBuild` proprietà. Vedere la documentazione relativa [compilazioni da riga di comando di xamarin. IOS in Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per altri dettagli sul `ServerAddress` e `ServerUser` proprietà.

* * *

## <a name="for-xamarin-3-and-earlier"></a>Per Xamarin 3 e versioni precedenti

L'estensione di Visual Studio Xamarin 3. x non fornisce un meccanismo per produrre `.xcarchive` archivi. Ciò premesso, la logica utilizzata per creare `.xcarchive` gli archivi di Xamarin Studio su Mac [descritto di seguito](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), pertanto è Impossibile creare la propria `.xcarchive` manualmente se desiderato.

Ma è importante notare che non è necessario un `.xcarchive` per inviare all'App Store. È possibile inviare un file IPA, purché sia firmato con un profilo di distribuzione di App Store (non un profilo distribuzione Ad Hoc).

Infatti, può anche solo comprimere il `.app` bundle (che viene firmato con un profilo distribuzione archiviare App) e inviare il `.zip` file all'archivio di app.

In entrambi i casi, è possibile utilizzare l'app Loader di applicazioni per inviare l'app (anziché Xcode).

