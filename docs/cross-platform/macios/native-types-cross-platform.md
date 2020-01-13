---
title: Utilizzo di tipi nativi nelle app multipiattaforma
description: Questo articolo illustra l'uso dei nuovi tipi nativi iOS API unificata (nint, nuint, nFloat) in un'applicazione multipiattaforma in cui il codice viene condiviso con dispositivi non iOS, ad esempio Android o Windows Phone sistemi operativi.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: davidortinau
ms.author: daortin
ms.date: 04/07/2016
ms.openlocfilehash: c86a00f325f9799b16f6244d3d1cb68de31be005
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015535"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Utilizzo di tipi nativi nelle app multipiattaforma

_Questo articolo illustra l'uso dei nuovi tipi nativi iOS API unificata (nint, nuint, nFloat) in un'applicazione multipiattaforma in cui il codice viene condiviso con dispositivi non iOS, ad esempio Android o Windows Phone sistemi operativi._

I tipi nativi di 64 tipi funzionano con le API iOS e Mac. Se si scrive codice condiviso eseguito anche in Android o Windows, sarà necessario gestire la conversione dei tipi unificati in tipi .NET normali che è possibile condividere.

In questo documento vengono illustrati diversi modi per interagire con i API unificata dal codice condiviso/comune.

## <a name="when-to-use-the-native-types"></a>Quando usare i tipi nativi

Le API unificate Xamarin.iOS e Xamarin.Mac includono ancora i tipi di dati `int`, `uint` e `float`, oltre ai tipi di `RectangleF`, `SizeF` e `PointF`. Questi tipi di dati esistenti devono continuare a essere usati in qualsiasi codice condiviso e multipiattaforma. I nuovi tipi di dati nativi devono essere usati solo quando si effettua una chiamata a un'API Mac o iOS in cui è necessario il supporto per i tipi compatibili con l'architettura.

A seconda della natura del codice condiviso, in alcuni casi potrebbe essere necessario che il codice multipiattaforma debba gestire i tipi di dati `nint`, `nuint` e `nfloat`. Ad esempio: una libreria che gestisce le trasformazioni su dati rettangolari che in precedenza utilizzavano `System.Drawing.RectangleF` per condividere le funzionalità tra le versioni Xamarin.iOS e Xamarin.Android di un'app, doveva essere aggiornata per gestire i tipi nativi in iOS.

Il modo in cui queste modifiche vengono gestite dipende dalle dimensioni e dalla complessità dell'applicazione e dalla modalità di condivisione del codice utilizzata, come si vedrà nelle sezioni seguenti.

## <a name="code-sharing-considerations"></a>Considerazioni sulla condivisione del codice

Come indicato nel documento relativo alle [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) , esistono due modi principali per condividere il codice tra progetti multipiattaforma: progetti condivisi e librerie di classi portabili. Quale dei due tipi è stato usato, limiterà le opzioni disponibili quando si gestiscono i tipi di dati nativi nel codice multipiattaforma.

### <a name="portable-class-library-projects"></a>Progetti libreria di classi portabile

Una libreria di classi portabile (PCL) consente di individuare le piattaforme che si desidera supportare e di utilizzare le interfacce per fornire funzionalità specifiche della piattaforma.

Poiché il tipo di progetto PCL viene compilato in un `.DLL` e non ha alcun significato per la API unificata, sarà necessario usare i tipi di dati esistenti (`int`, `uint`, `float`) nel codice sorgente della libreria di classi portabile e digitare il cast delle chiamate alle classi di PCL metodi e nelle applicazioni front-end. Esempio:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Progetti condivisi

Il tipo di progetto asset condiviso consente di organizzare il codice sorgente in un progetto separato che viene quindi incluso e compilato nelle singole app front-end specifiche della piattaforma e USA `#if` direttive del compilatore come richiesto per gestire le specifiche della piattaforma requisiti.

Quando si sceglie il metodo di supporto per i tipi di dati nativi in una multipiattaforma, è necessario tenere conto delle dimensioni e della complessità delle applicazioni per dispositivi mobili front-end che utilizzano codice condiviso, insieme alle dimensioni e alla complessità del codice da condividere. Progetto asset condiviso.

In base a questi fattori, i tipi di soluzioni seguenti possono essere implementati utilizzando le direttive del compilatore `if __UNIFIED__ ... #endif` per gestire le modifiche API unificata specifiche del codice.

#### <a name="using-duplicate-methods"></a>Uso di metodi duplicati

Eseguire l'esempio di una raccolta che esegue trasformazioni sui dati rettangolari specificati sopra. Se la libreria contiene solo uno o due metodi molto semplici, è possibile scegliere di creare versioni duplicate di questi metodi per Xamarin.iOS e Xamarin.Android. Esempio:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

Nel codice precedente, poiché la routine `CalculateArea` è molto semplice, è stata usata la compilazione condizionale e è stata creata una versione separata API unificata del metodo. D'altra parte, se la libreria conteneva molte routine o diverse routine complesse, questa soluzione non sarebbe fattibile, in quanto rappresenterebbe un problema per mantenere sincronizzati tutti i metodi per le modifiche o le correzioni di bug.

#### <a name="using-method-overloads"></a>Uso degli overload del metodo

In tal caso, è possibile che la soluzione crei una versione di overload dei metodi usando i tipi di dati a 32 bit, in modo che ora `CGRect` come parametro e/o un valore restituito, convertire il valore in una `RectangleF` (sapendo che la conversione da `nfloat` a `float` è una conversione con perdita di perdite) e chiama la versione originale della routine per eseguire il lavoro effettivo. Esempio:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Call original routine to calculate area
                return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

Anche in questo caso, si tratta di una soluzione efficace purché la perdita di precisione non influisca sui risultati per le esigenze specifiche dell'applicazione.

#### <a name="using-alias-directives"></a>Uso delle direttive alias

Per le aree in cui la perdita di precisione rappresenta un problema, un'altra possibile soluzione consiste nell'usare `using` direttive per creare un alias per i tipi di dati nativi e CoreGraphics includendo il codice seguente nella parte superiore dei file di codice sorgente condivisi e convertendo gli eventuali @no_ necessari valori di _t_1_, `uint` o `float` per `nint`, `nuint` o `nfloat`:

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

Quindi, il codice di esempio diventa:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Map Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Map Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

Si noti che in questo caso è stato modificato il metodo `CalculateArea` per restituire un `nfloat` anziché il `float`standard. Questa operazione è stata eseguita in modo da evitare un errore di compilazione durante il tentativo di convertire in _modo implicito_ il risultato `nfloat` del calcolo (poiché entrambi i valori vengono moltiplicati sono di tipo `nfloat`) in un `float` valore restituito.

Se il codice viene compilato ed eseguito in un dispositivo non API unificata, il `using nfloat = global::System.Single;` esegue il mapping della `nfloat` a una `Single` che verrà convertita in modo implicito in un `float` consentendo all'applicazione front-end che utilizza il metodo di `CalculateArea` di chiamare il metodo di senza apportare modifiche.

#### <a name="using-type-conversions-in-the-front-end-app"></a>Uso delle conversioni di tipi nell'app front-end

Nel caso in cui le applicazioni front-end eseguano solo un numero limitato di chiamate alla libreria di codice condivisa, un'altra soluzione potrebbe essere lasciare invariata la libreria e eseguire il cast del tipo nell'applicazione Xamarin.iOS o Xamarin.Mac quando si chiama la routine esistente. Esempio:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Se l'applicazione consumer effettua centinaia di chiamate alla libreria di codice condivisa, anche in questo caso potrebbe non essere una soluzione corretta.

In base all'architettura dell'applicazione, potrebbe essere necessario usare una o più delle soluzioni precedenti per supportare i tipi di dati nativi (dove necessario) nel codice multipiattaforma.

## <a name="xamarinforms-applications"></a>Applicazioni Xamarin.Forms

Per usare Xamarin.Forms per le interfacce utente multipiattaforma che saranno condivise anche con un'applicazione API unificata, è necessario quanto segue:

- L'intera soluzione deve usare la versione 1.3.1 (o successiva) del pacchetto NuGet Xamarin.Forms.
- Per tutti i rendering personalizzati di Xamarin.iOS, usare gli stessi tipi di soluzioni presentati in precedenza in base alla modalità di condivisione del codice dell'interfaccia utente (progetto condiviso o PCL).

Come in un'applicazione standard multipiattaforma, i tipi di dati a 32 bit esistenti devono essere usati in qualsiasi codice condiviso e multipiattaforma per la maggior parte delle situazioni. I nuovi tipi di dati nativi devono essere usati solo quando si effettua una chiamata a un'API Mac o iOS in cui è necessario il supporto per i tipi compatibili con l'architettura.

Per altri dettagli, vedere la documentazione relativa all' [aggiornamento delle app Xamarin.Forms esistenti](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) .

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato quando utilizzare i tipi di dati nativi in un'applicazione API unificata e le relative implicazioni multipiattaforma. Sono state presentate diverse soluzioni che possono essere usate nelle situazioni in cui è necessario usare i nuovi tipi di dati nativi nelle librerie multipiattaforma. È stata anche illustrata una guida rapida per supportare le API unificate nelle applicazioni multipiattaforma Xamarin.Forms.

## <a name="related-links"></a>Collegamenti correlati

- [API unificata](~/cross-platform/macios/unified/index.md)
- [Tipi nativi](~/cross-platform/macios/nativetypes.md)
- [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md)
- [Esempio di condivisione del codice](https://docs.microsoft.com/samples/xamarin/mobile-samples/sharingcode/)
