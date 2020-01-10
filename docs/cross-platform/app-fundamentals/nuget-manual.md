---
title: Creazione manuale di pacchetti NuGet per Novell
description: Questo documento contiene suggerimenti per la creazione di pacchetti NuGet destinati alla piattaforma Novell. Vengono descritti i profili Novell del pacchetto NuGet, NuGet PCL con dipendenze della piattaforma e collegamenti a diversi esempi Open Source.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 16b8f303555bc2f45516c3c060c0d2482f9c4954
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728226"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Creazione manuale di pacchetti NuGet per Novell

_Questa pagina contiene alcuni suggerimenti per facilitare la compilazione di pacchetti NuGet destinati alla piattaforma Novell._

> [!NOTE]
> Xamarin Studio 6,2 (e Visual Studio per Mac) include la possibilità di generare _automaticamente_ pacchetti NuGet dai progetti PCL, .NET standard o Shared. Per altri dettagli, vedere la Guida alle [librerie multipiattaforma per la condivisione del codice](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) .

## <a name="nuget-package-xamarin-profiles"></a>Profili Novell del pacchetto NuGet

Il sito Web NuGet che [supporta più versioni e profili di .NET Framework](https://docs.nuget.org/create/enforced-package-conventions) illustra come supportare diversi Framework e profili Microsoft, ma non include i nomi dei framework di destinazione usati da Novell.

I principali framework di destinazione Novell attualmente in uso sono i seguenti:

- **MonoAndroid** - Xamarin.Android
- **Novell. iOS** -Novell. iOS [API unificata](~/cross-platform/macios/unified/index.md) (supporta 64 bit)
- **Novell. Mac** : profilo mobile di Novell. Mac, equivalente alla superficie dell'API Novell. iOS e Novell. Android.

Esiste anche una destinazione per la [API classica](~/cross-platform/macios/unified/index.md)iOS precedente:

- **MonoTouch** -iOS API classica

Un file con **estensione NuSpec** che ha come destinazione tutti questi elementi avrà un aspetto simile al seguente:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

Il codice precedente ignora tutte le librerie di classi portabili.

La maggior parte dei file con **estensione NuSpec** specifica il numero di versione del Framework di destinazione, ma è facoltativa se l'assembly funziona con tutte le versioni di tale framework di destinazione. Quindi, se la destinazione è **lib\MonoAndroid** , questo significa che funziona con qualsiasi versione di Novell. Android.

È possibile specificare la versione con un set di numeri senza un separatore decimale oppure è possibile specificarla usando i punti decimali. Senza il separatore decimale, NuGet accetta solo ogni numero e lo trasforma in una versione inserendo un carattere ' .' tra le cifre.

Nella sezione precedente "MonoAndroid10" significa "Android 1,0". Questo significa solo che il [Framework di destinazione](~/android/app-fundamentals/android-api-levels.md) del progetto deve essere monoandroid versione 1,0 o successiva. La versione viene specificata nell'elemento `<TargetFrameworkVersion>` nel file di progetto.

Per chiarire:

- **MonoAndroid403** corrisponde a Android 4.0.3 e versioni successive (livello API IE 15)
- **Novell. iOS10** corrisponde a Novell. iOS 1,0 e versioni successive
- **Novell. iOS 1.0** corrisponde anche a Novell. iOS 1,0 e versioni successive

## <a name="pcl-nugets-with-platform-dependencies"></a>NuGet di PCL con dipendenze di piattaforma

I profili PCL sono limitati alle API .NET Framework a cui possono accedere e certamente non possono accedere al codice specifico della piattaforma. Questi collegamenti di terze parti illustrano diversi approcci per la creazione di pacchetti NuGet che usano le API PCL e native per garantire la compatibilità per Novell e altre piattaforme:

- [Come usare le librerie di classi portabili](https://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [Il trucco per le esche e le opzioni PCL](https://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Creazione di una PCL NuGet che funziona con Novell. iOS](https://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Questo [elenco esterno dei profili PCL con il nome di destinazione NuGet](https://portablelibraryprofiles.stephencleary.com) è anche un utile riferimento.

## <a name="examples"></a>Esempi

Alcuni esempi Open Source a cui è possibile fare riferimento:

- [**ModernHttpClient**](https://www.nuget.org/packages/modernhttpclient/) : scrivere l'app usando System .NET. http, ma eliminare questa libreria in e sarà notevolmente più veloce (visualizzazione dell' [origine](https://github.com/paulcbetts/ModernHttpClient)).
- [**Splat**](https://www.nuget.org/packages/Splat/) : una libreria che consente di rendere le cose multipiattaforma che devono essere (visualizzazione [origine](https://github.com/paulcbetts/Splat)).
- [**NGraphics**](https://www.nuget.org/packages/NGraphics/) : libreria multipiattaforma per il rendering di grafica vettoriale in .NET (visualizzazione [origine](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Collegamenti correlati

- [Nugetizer-3000 creazione automatica di NuGet](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)       
- [Inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
