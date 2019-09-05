---
title: È possibile creare un archivio con estensione xcarchive da Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 6d35827b00a4ccc9bbe3e71444536425e4e1c3b1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288116"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>È possibile creare un archivio con estensione xcarchive da Visual Studio?

## <a name="for-xamarin-4"></a>Per Novell 4

A partire da Novell 4. x, è ora possibile creare un oggetto `.xcarchive` da Windows impostando la `ArchiveOnBuild` proprietà su `true`. Ad esempio, usando `MSBuild` nella riga di comando:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

Il `.xcarchive` verrà inserito `$HOME/Library/Developer/Xcode/Archives` nella directory dell'host di compilazione Mac che sia Xcode che Xamarin studio ricerca per visualizzare gli archivi compilati in precedenza.

Per alcune brevi note aggiuntive sulla `ArchiveOnBuild` proprietà, vedere il post dei [forum Novell](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) . Per ulteriori informazioni sulle `ServerAddress` proprietà e, `ServerUser` vedere la documentazione relativa alle [compilazioni da riga di comando di Novell. iOS in Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) .

* * *

## <a name="for-xamarin-3-and-earlier"></a>Per Novell 3 e versioni precedenti

L'estensione Novell 3. x di Visual Studio non fornisce un meccanismo per la `.xcarchive` produzione di archivi. Ciò premesso, la logica usata per `.xcarchive` creare gli archivi in Xamarin studio su Mac [viene descritta qui](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), quindi è possibile crearla `.xcarchive` manualmente, se si vuole.

Tuttavia, è importante notare che non è necessario un `.xcarchive` da inviare all'App Store. È possibile inviare un file IPA purché sia firmato con un profilo di distribuzione dell'app Store (non un profilo di distribuzione ad hoc).

Infatti, è anche possibile semplicemente comprimere il `.app` Bundle (firmato con un profilo di distribuzione dell'app Store) e `.zip` inviare il file all'App Store.

In entrambi i casi, è possibile usare l'app del caricatore di applicazioni per inviare l'app, anziché Xcode.

