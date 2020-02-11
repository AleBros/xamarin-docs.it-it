---
title: È possibile creare un archivio con estensione xcarchive da Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 27697223735c4074dd508d3f603ef6aa4e47b1be
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031178"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>È possibile creare un archivio con estensione xcarchive da Visual Studio?

## <a name="for-xamarin-4"></a>Per Xamarin 4

A partire da Xamarin 4. x, è ora possibile creare un `.xcarchive` da Windows impostando la proprietà `ArchiveOnBuild` su `true`. Ad esempio, usando `MSBuild` nella riga di comando:

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

Il `.xcarchive` verrà inserito nella directory `$HOME/Library/Developer/Xcode/Archives` nell'host di compilazione Mac che sia Xcode che Xamarin Studio la ricerca per visualizzare archivi compilati in precedenza.

Per alcune brevi note aggiuntive sulla proprietà `ArchiveOnBuild`, vedere il [post dei forum di Xamarin](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635) . Per ulteriori informazioni sulle proprietà `ServerAddress` e `ServerUser`, vedere la documentazione relativa alle [compilazioni da riga di comando di Xamarin.iOS in Windows](~/ios/get-started/installation/windows/connecting-to-mac/index.md) .

* * *

## <a name="for-xamarin-3-and-earlier"></a>Per Xamarin 3 e versioni precedenti

L'estensione di Visual Studio Xamarin 3. x non fornisce un meccanismo per produrre archivi `.xcarchive`. Ciò premesso, la logica usata per creare archivi `.xcarchive` in Xamarin Studio su Mac [viene descritta qui](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5), quindi è possibile creare `.xcarchive` manualmente se si desidera.

Tuttavia, è importante notare che non è necessario un `.xcarchive` da inviare all'App Store. È possibile inviare un file IPA purché sia firmato con un profilo di distribuzione dell'app Store (non un profilo di distribuzione ad hoc).

Infatti, è anche possibile semplicemente comprimere il pacchetto di `.app` (firmato con un profilo di distribuzione dell'app Store) e inviare il file di `.zip` all'App Store.

In entrambi i casi, è possibile usare l'app del caricatore di applicazioni per inviare l'app, anziché Xcode.
