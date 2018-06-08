---
title: Creazione manuale di pacchetti NuGet di Xamarin
description: Questo documento contiene suggerimenti per la creazione di pacchetti NuGet destinati alla piattaforma Xamarin. Descrive i profili Xamarin pacchetto NuGet, NuGets libreria di classi Portabile con le dipendenze della piattaforma e collegamenti a diversi esempi di open source.
ms.prod: xamarin
ms.assetid: a5964686-5fc6-4280-b087-7ba27cc1c8bf
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 2adfe70e1d37c7f6e6fc825de1d86513de4a985c
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34845967"
---
# <a name="manually-creating-nuget-packages-for-xamarin"></a>Creazione manuale di pacchetti NuGet di Xamarin

_In questa pagina sono riportati alcuni suggerimenti per la creazione di pacchetti NuGet destinati alla piattaforma Xamarin._

> [!NOTE]
> Xamarin Studio 6.2 (e Visual Studio per Mac) include la possibilità di _automaticamente_ generare pacchetti NuGet PCL, .NET Standard o progetti condivisi. Consultare la [librerie multipiattaforma per la condivisione del codice](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md) Guida per ulteriori dettagli.

## <a name="nuget-package-xamarin-profiles"></a>Pacchetto NuGet Xamarin profili

Il sito Web NuGet [che supporta più versioni di .NET Framework e i profili](https://docs.nuget.org/create/enforced-package-conventions) viene illustrato come supportare diversi framework di Microsoft e i profili ma non include i nomi di framework di destinazione usati da Xamarin.

Il framework di destinazione Xamarin principali in uso oggi sono:

* **MonoAndroid** - Xamarin.Android
* **Xamarin** -xamarin [Unified API](~/cross-platform/macios/unified/index.md) (supporto a 64 bit)
* **Xamarin.Mac** -profilo mobile del Xamarin.Mac, che è equivalente all'area di xamarin. IOS e xamarin API.

È inoltre disponibile una destinazione per il file di iOS precedente [API classica](~/cross-platform/macios/unified/index.md):

* **MonoTouch** -iOS API classica

Oggetto **. nuspec** file destinato a tutti questi sarebbe simile al seguente:

```xml
<files>
    <file src="Mac\bin\Release\*.dll" target="lib\Xamarin.Mac20" />
    <file src="iOS\bin\Release\*.dll" target="lib\Xamarin.iOS10" />
    <file src="Android\bin\Release\*.dll" target="lib\MonoAndroid10" />
    <file src="iOSClassic\bin\Release\*.dll" target="lib\MonoTouch10" />
</files>
```

Il precedente Ignora tutte le librerie di classi portabile.

La maggior parte delle **. nuspec** file specificano il numero di versione del framework di destinazione, ma è facoltativo se l'assembly funziona con tutte le versioni di tale framework di destinazione. Pertanto, se la destinazione è stato **lib\MonoAndroid** ciò significa funziona con qualsiasi versione di xamarin.

È possibile specificare la versione con un set di numeri senza un separatore decimale o è possibile specificare utilizzando cifre decimali. Senza il separatore decimale NuGet appena accetta ogni numero e trasformarlo in una versione inserendo un '.' tra ogni cifra.

Nel precedente "MonoAndroid10" significa "Android 1.0". Ciò significa solo il progetto [framework di destinazione](~/android/app-fundamentals/android-api-levels.md) deve essere MonoAndroid versione 1.0 o versione successiva. La versione specificata nel `<TargetFrameworkVersion>` elemento nel file di progetto.

Per chiarire:

- **MonoAndroid403** corrisponde Android è 4.0.3 e versioni successive (ad esempio API livello 15)
- **Xamarin.iOS10** corrisponde xamarin 1.0 e successive
- **Xamarin.iOS1.0** corrisponde anche xamarin 1.0 e successive

## <a name="pcl-nugets-with-platform-dependencies"></a>Libreria di classi Portabile NuGets con le dipendenze della piattaforma

PCL profili sono limitati nel quale possono accedere API di .NET framework e non possono accedere sicuramente codice specifico della piattaforma. Questi collegamenti 3rd party illustrano approcci diversi per la creazione di pacchetti NuGet che utilizzano API native e libreria di classi Portabile per garantire la compatibilità per altre piattaforme e Xamarin:

- [Come rendere lavoro librerie di classi portabile per l'utente](http://blogs.msdn.com/b/dsplaisted/archive/2012/08/27/how-to-make-portable-class-libraries-work-for-you.aspx)
- [Lo stratagemma PCL tecnica dello](http://log.paulbetts.org/the-bait-and-switch-pcl-trick/)
- [Creazione di una PCL NuGet che funziona con xamarin](http://www.jimbobbennett.io/creating-a-nuget-pcl-that-works-with-xamarin-ios/)

Questo esterno [elenco dei profili di libreria di classi Portabile con il nome della destinazione NuGet](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) è anche un utile riferimento.

## <a name="examples"></a>Esempi

Alcuni esempi di open source che è possibile fare riferimento a:

- [**ModernHttpClient** ](https://www.nuget.org/packages/modernhttpclient/) : scrivere un'applicazione con System.NET. http, ma eliminare questa raccolta in e spostarlo notevolmente più veloce (visualizzazione [origine](https://github.com/paulcbetts/ModernHttpClient)).
- [**Gocce** ](https://www.nuget.org/packages/Splat/) : una libreria multipiattaforma il problema che deve essere (visualizzazione [origine](https://github.com/paulcbetts/Splat)).
- [**NGraphics** ](https://www.nuget.org/packages/NGraphics/) -una libreria multipiattaforma per il rendering di grafica vettoriale su .NET (visualizzazione [origine](https://github.com/praeclarum/NGraphics/blob/master/NGraphics.nuspec)).

## <a name="related-links"></a>Collegamenti correlati

- [Nugetizer 3000 automatizzata creazione Nuget](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)
- [Aggiornamento NuGets per iOS a 64 bit](http://blog.xamarin.com/how-to-update-nuget-packages-for-64-bit/)
- [Tra cui un NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
