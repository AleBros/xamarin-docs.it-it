---
title: Utilizzo di tipi nativi nelle App multipiattaforma
description: In questo articolo viene illustrato l'utilizzo di iOS nuovi tipi Unified API Native (nint, nuint, nfloat) in un'applicazione multipiattaforma in cui il codice è condiviso con i dispositivi non iOS, ad esempio Android o sistemi operativi Windows Phone.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: eb6979691eeef6dd7436d74fdfd501c747d9b3c6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Utilizzo di tipi nativi nelle App multipiattaforma

_In questo articolo viene illustrato l'utilizzo di iOS nuovi tipi Unified API Native (nint, nuint, nfloat) in un'applicazione multipiattaforma in cui il codice è condiviso con i dispositivi non iOS, ad esempio Android o sistemi operativi Windows Phone._


I tipi nativi 64 tipi compatibili con iOS e Mac API. Se si sta scrivendo codice condiviso che viene eseguito anche in Android o Windows, è necessario gestire la conversione dei tipi unificato in normale di tipi .NET che è possibile condividere.

Questo documento vengono illustrati diversi modi per interagire con l'API unificata dal codice comune condiviso.

## <a name="when-to-use-the-native-types"></a>Quando utilizzare i tipi nativi

Xamarin. IOS e API unificata Xamarin.Mac ancora includere i `int`, `uint` e `float` tipi di dati, nonché il `RectangleF`, `SizeF` e `PointF` tipi. Questi tipi di dati esistenti continueranno a essere utilizzato in qualsiasi codice condiviso tra piattaforme diverse. Nuovi tipi di dati nativo devono essere utilizzati solo quando si effettua una chiamata a un'API di Mac o iOS in cui il supporto per i tipi compatibili con architettura sono necessari.

A seconda della natura del codice condiviso, potrebbe esserci volte in cui potrebbe essere necessario codice multipiattaforma di affrontare il `nint`, `nuint` e `nfloat` tipi di dati. Ad esempio: una libreria che gestisce trasformazioni ai dati rettangolare utilizzato in precedenza `System.Drawing.RectangleF` per condividere funzionalità tra le versioni di xamarin. IOS e xamarin di un'app, dovranno essere aggiornate per gestire i tipi nativi in iOS.

La modalità di gestione di queste modifiche dipende le dimensioni e la complessità dell'applicazione e il modulo di codice la condivisione che è stato utilizzato, come verrà illustrato nelle sezioni riportate di seguito.

## <a name="code-sharing-considerations"></a>Considerazioni sulla condivisione del codice

Come indicato nella [opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) documento, sono disponibili due metodi principali per la condivisione di codice tra progetti multipiattaforma: progetti condivisi e librerie di classi portabili. Quale dei due tipi è stata utilizzata, si limiterà le opzioni che disponibili quando si gestiscono i tipi di dati nativo nel codice multipiattaforma.

### <a name="portable-class-library-projects"></a>Progetti libreria di classi portabile

Una libreria di classe portabile (PCL) consente di eseguire le piattaforme che si desidera supportare e utilizzare le interfacce per fornire funzionalità specifiche della piattaforma di destinazione.

Poiché il tipo di progetto libreria di classi Portabile viene compilato verso il basso per un `.DLL` e non ha è appropriata dell'API unificata, verrà forzato per continuare a utilizzare i tipi di dati esistente (`int`, `uint`, `float`) nella libreria di classi Portabile codice sorgente e tipo cast le chiamate al PCLs le classi e metodi nelle applicazioni front-end. Di seguito è riportato un esempio:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Progetti condivisi

Il tipo di progetto Asset condiviso consente di organizzare codice sorgente in un progetto separato che quindi ottiene incluso e compilato le app di singole specifiche della piattaforma front-end e utilizzare `#if` come necessarie per gestire le direttive del compilatore requisiti specifici della piattaforma.

Le dimensioni e la complessità della parte anteriore terminare applicazioni per dispositivi mobili che utilizzano il codice condiviso, con le dimensioni e la complessità del codice condiviso, è necessario tenere conto quando si sceglie il metodo di supporto per dati nativi tipi multipiattaforma con la Tipo di progetto Asset condiviso.

In base a questi fattori, i seguenti tipi di soluzioni potrebbero essere implementati utilizzando il `if __UNIFIED__ ... #endif` direttive del compilatore di gestire le modifiche specifiche Unified API al codice.

#### <a name="using-duplicate-methods"></a>Utilizzo di metodi duplicati

Nell'esempio di una libreria che esegue le trasformazioni ai dati rettangolare specificati in precedenza. Se la libreria contiene solo uno o due metodi molto semplici, è possibile scegliere di creare versioni duplicate di questi metodi per xamarin. IOS e xamarin. Ad esempio:

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

Nel codice precedente, poiché il `CalculateArea` routine è molto semplice, abbiamo utilizzato la compilazione condizionale e creato un separato e versione dell'API unificata del metodo. D'altra parte, se la libreria contiene molte routine o diverse routine complesse, questa soluzione non sarebbe fattibile, come potrebbero presentarsi un problema di sincronizzazione tutti i metodi per le modifiche o correzioni di bug.

#### <a name="using-method-overloads"></a>Overload di metodo

In tal caso, la soluzione potrebbe essere possibile creare una versione di overload dei metodi mediante i tipi di dati a 32 bit in modo che ora `CGRect` come parametro e/o valore restituito, convertire il valore a un `RectangleF` (sapere che la conversione da `nfloat` a `float` è una conversione di perdita di dati) e chiamare la versione originale della routine per eseguire il lavoro effettivo. Ad esempio:

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

Anche questa è una buona soluzione, purché la perdita di precisione non influenza i risultati per esigenze specifiche dell'applicazione.

#### <a name="using-alias-directives"></a>Alias direttive using

Per le aree in cui la perdita di precisione è un problema, un'altra possibile soluzione consiste nell'utilizzare `using` direttive per creare un alias per i tipi di dati nativo e CoreGraphics includendo il codice seguente all'inizio del file di codice sorgente condiviso e qualsiasi conversione necessario `int`, `uint` o `float` valori `nint`, `nuint` o `nfloat`:

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

In modo che diventa quindi il codice di esempio:

```csharp
using System;
using System.Drawing;

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

Si noti che questo è stato modificato il `CalculateArea` metodo viene restituito un `nfloat` anziché lo standard `float`. Questa operazione viene eseguita in modo che non si ottengono un errore di compilazione sta tentando di _in modo implicito_ convertire il `nfloat` risultato di questo calcolo (poiché sono entrambi i valori viene moltiplicati `nfloat`) in un `float` valore restituito.

Se il codice viene compilato ed eseguito in un dispositivo non Unified API, il `using nfloat = global::System.Single;` esegue il mapping di `nfloat` per un `Single` che verrà convertito in modo implicito un `float` consentendo all'applicazione front-end dispendiosa in termini di chiamare il `CalculateArea` metodo senza modifica.


#### <a name="using-type-conversions-in-the-front-end-app"></a>Usa le conversioni di tipo nell'applicazione di Front-End

Nel caso in cui le applicazioni front-end apportano solo un numero limitato di chiamate alla libreria di codice condiviso, un'altra soluzione è possibile lasciare la libreria invariata e cast di tipo nell'applicazione di xamarin. IOS o Xamarin.Mac quando si chiama la routine esistente. Di seguito è riportato un esempio:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Se l'applicazione consumer apporta centinaia di chiamate alla libreria di codice condiviso, nuovo, potrebbe non trattarsi di una buona soluzione.

Basata sull'architettura dell'applicazione, è possibile comporta l'utilizzo di uno o più delle soluzioni sopra per supportare i dati nativi tipi (se richiesto) nel codice multipiattaforma.


## <a name="xamarinforms-applications"></a>Applicazioni di xamarin. Forms

Per utilizzare xamarin. Forms per le interfacce utente multipiattaforma che verranno condivisi anche con un'applicazione API unificata è necessario quanto segue:

- L'intera soluzione deve usare versione 1.3.1 (o versione successiva) del pacchetto NuGet di xamarin. Forms.
- Per qualsiasi rendering personalizzato xamarin. IOS, usare gli stessi tipi di soluzioni presentate in precedenza in base a come il codice dell'interfaccia utente è stato condiviso (progetto condiviso o PCL).

Come in un'applicazione standard multipiattaforma, 32 bit esistente tipi di dati da utilizzare in qualsiasi codice condiviso tra piattaforme diverse per la maggior parte di tutte le situazioni. Nuovi tipi di dati nativo deve essere utilizzati solo quando si effettua una chiamata a un'API di Mac o iOS in cui il supporto per i tipi compatibili con architettura sono necessari.

Per ulteriori informazioni, vedere il nostro [aggiornamento App xamarin. Forms esistenti](http://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/) documentazione.

## <a name="summary"></a>Riepilogo

In questo articolo è necessario vedere quando i tipi di dati nativi in un'applicazione API unificata e i relativi multipiattaforma implicazioni da utilizzare. Che abbiamo presentato diverse soluzioni che possono essere utilizzate nelle situazioni in cui i nuovi tipi di dati nativo deve essere utilizzati nelle librerie multipiattaforma. E che abbiamo visto Guida rapida per il supporto di Unified API nelle applicazioni multipiattaforma con xamarin. Forms.



## <a name="related-links"></a>Collegamenti correlati

- [API unificata](~/cross-platform/macios/unified/index.md)
- [Tipi nativi](~/cross-platform/macios/nativetypes.md)
- [Sharing Code Options](~/cross-platform/app-fundamentals/code-sharing.md)
- [Nell'esempio di codice la condivisione](https://developer.xamarin.com/samples/mobile/SharingCode/)
