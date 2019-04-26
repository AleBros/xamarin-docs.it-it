---
title: Creazione manuale di pacchetti NuGet per Xamarin
description: Questo documento contiene suggerimenti per la creazione di pacchetti NuGet destinati alla piattaforma Xamarin. Descrive i profili di Xamarin di pacchetti NuGet, pacchetti NuGet di libreria di classi Portabile con dipendenze di piattaforma e i collegamenti a vari esempi open source.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 4f4ca327479a7f4eb4a7dc7feafdd71291c1b7fe
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61186143"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Creazione manuale di pacchetti NuGet per Xamarin

_In questa pagina sono riportati alcuni suggerimenti per la creazione di pacchetti NuGet destinati alla piattaforma Xamarin._

> [!NOTE]
> Xamarin Studio 6.2 (e Visual Studio per Mac) include la possibilità _automaticamente_ generare pacchetti NuGet da libreria di classi Portabile, .NET Standard o progetti condivisi. Vedere le [librerie multipiattaforma per la condivisione del codice](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) Guida per altre informazioni.

## <a name="nuget-package-xamarin-profiles"></a>I profili di pacchetto NuGet Xamarin

Il sito Web NuGet [che supporta più versioni di .NET Framework e i profili](https://docs.nuget.org/create/enforced-package-conventions) vengono trattati come supportare diversi framework di Microsoft e i profili ma non include i nomi dei framework di destinazione usati da Xamarin.

I framework di destinazione principali di Xamarin in uso oggi sono:

* **MonoAndroid** - Xamarin.Android
* **Xamarin. IOS** -xamarin. IOS [API unificata](~/cross-platform/macios/unified/index.md) (supporto a 64 bit)
* **Xamarin. Mac** -profilo per dispositivi mobili di xamarin. Mac, che equivale alla superficie API xamarin. Android e xamarin. IOS.

È inoltre disponibile una destinazione per iOS meno recenti [API classica](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -iOS API classica

Oggetto **file con estensione nuspec** file tutti questi elementi come destinazione sarebbe simile al:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

Il codice precedente Ignora tutte le librerie di classi portabile.

La maggior parte degli **file con estensione nuspec** file specificano il numero di versione del framework di destinazione, ma è facoltativo se l'assembly è compatibile con tutte le versioni di tale framework di destinazione. Pertanto, se la destinazione è stata **lib\MonoAndroid** ciò significa funziona con qualsiasi versione di xamarin. Android.

È possibile specificare la versione con un set di numeri senza un separatore decimale oppure è possibile specificarla usando i separatori decimali. Senza il separatore decimale NuGet appena accetta ogni numero e trasformarlo in una versione inserendo un '.' tra ogni cifra.

Nel precedente "MonoAndroid10" significa "Android 1.0". Ciò significa solo che il progetto [framework di destinazione](~/android/app-fundamentals/android-api-levels.md) deve essere MonoAndroid versione 1.0 o versione successiva. La versione specificata nel `<TargetFrameworkVersion>` elemento nel file di progetto.

Per chiarire:

- **MonoAndroid403** corrisponde a Android 4.0.3 e versioni più recenti (ad esempio API livello 15)
- **Xamarin.iOS10** corrisponde a xamarin. IOS 1.0 e successive
- **Xamarin.iOS1.0** corrisponde anche a xamarin. IOS 1.0 e successive

## <a name="pcl-nugets-with-platform-dependencies"></a>Pacchetti NuGet di libreria di classi Portabile con dipendenze di piattaforma

I profili di libreria di classi Portabile sono limitati nelle quali possono accedere le API di .NET framework e non potranno accedere senz'altro codice specifico della piattaforma. Questi collegamenti 3rd-party illustrano diversi approcci per la creazione di pacchetti NuGet che usano API native e libreria di classi Portabile per garantire la compatibilità per Xamarin e altre piattaforme:

- [Come usare le librerie di classi portabile per l'utente](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [Lo stratagemma di libreria di classi Portabile specchietto per le allodole](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Creazione di un NuGet PCL che funziona con xamarin. IOS](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Questo esterno [elenco dei profili di libreria di classi Portabile con il nome della destinazione NuGet](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) è anche un riferimento utile.

## <a name="examples"></a>Esempi

Alcuni esempi open source che è possibile fare riferimento a:

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) : scrivere l'app tramite System.NET. http, ma eliminare questa raccolta in e si passerà notevolmente più veloce (visualizzazione [origine](https://github.com/paulcbetts/ModernHttpClient)).
- [**Con carattere jolly** ](https://www.nuget.org/packages/Splat/) : una libreria per rendere le cose multipiattaforma che deve essere (visualizzazione [origine](https://github.com/paulcbetts/Splat)).
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -una libreria multipiattaforma per il rendering di grafica vettoriale su .NET (visualizzazione [origine](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Collegamenti correlati

- [Nugetizer 3000 automatizzati di creazione di Nuget](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)
- [L'aggiornamento di pacchetti NuGet per iOS a 64 bit](https://blog.xamarin.com/how-to-update-nuget-packages-for-64-bit/)
- [Inserimento di pacchetto NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
