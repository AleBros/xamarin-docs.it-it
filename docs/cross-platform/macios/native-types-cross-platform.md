---
title: Utilizzo di tipi nativi nelle app multipiattaforma
description: Questo articolo illustra l'uso di iOS nuovi tipi Unified API Native (nint, nuint, nfloat) in un'applicazione multipiattaforma in cui il codice è condiviso con i dispositivi non iOS, ad esempio Android o sistemi operativi Windows Phone.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: 489d2a76e6eff661360b24d1872ed1343c74b85e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261181"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Utilizzo di tipi nativi nelle app multipiattaforma

_Questo articolo illustra l'uso di iOS nuovi tipi Unified API Native (nint, nuint, nfloat) in un'applicazione multipiattaforma in cui il codice è condiviso con i dispositivi non iOS, ad esempio Android o sistemi operativi Windows Phone._


I tipi nativi 64 tipi funzionano con le API di Mac e iOS. Se si scrive codice condiviso che viene eseguito anche su Android o Windows, è necessario gestire la conversione di tipi unificato in tipi regolari di .NET che è possibile condividere.

Questo documento illustra diversi modi per interagire con l'API unificata dal codice o comune condiviso.

## <a name="when-to-use-the-native-types"></a>Quando usare i tipi nativi

API unificata di xamarin. Mac e xamarin. IOS includono ancora la `int`, `uint` e `float` tipi di dati, nonché il `RectangleF`, `SizeF` e `PointF` tipi. Questi tipi di dati esistenti continueranno a essere usato in qualsiasi codice condiviso e lo sviluppo multipiattaforma. I nuovi tipi di dati nativo devono essere utilizzati solo quando si effettua una chiamata a un'API iOS o Mac in cui il supporto per i tipi compatibili con architettura sono necessari.

A seconda della natura del codice condivisa, a volte può essere in codice multipiattaforma potrebbe essere necessario considerare con i `nint`, `nuint` e `nfloat` i tipi di dati. Ad esempio: una libreria che gestisce trasformazioni sui dati rettangolare che in precedenza usava `System.Drawing.RectangleF` per condividere funzionalità tra le versioni di xamarin. IOS e xamarin. Android di un'app, dovrà essere aggiornata per gestire i tipi nativi su iOS.

Come vengono gestite le modifiche varia a seconda della dimensione e complessità dell'applicazione e il modulo del codice di condivisione che è stato usato, come illustrato nelle sezioni seguenti.

## <a name="code-sharing-considerations"></a>Considerazioni sulla condivisione del codice

Come indicato nella [opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md) del documento, esistono due modi principali per condividere codice tra progetti multipiattaforma: Condividere i progetti e librerie di classi portabili. Quale dei due tipi è stata utilizzata, si limiterà le opzioni che disponibili quando si gestiscono i tipi di dati nativi in codice multipiattaforma.

### <a name="portable-class-library-projects"></a>Progetti libreria di classi portabile

Una libreria di classi portabile (PCL) consente di eseguire le piattaforme che si desidera supportare e usare le interfacce per fornire funzionalità specifiche della piattaforma di destinazione.

Poiché il tipo di progetto libreria di classi Portabile è compilato verso il basso in un `.DLL` e non ha alcun significato dell'API unificata, saranno costretti a continuare a usare i tipi di dati esistente (`int`, `uint`, `float`) libreria di classi Portabile il codice sorgente e il tipo viene eseguito il cast le chiamate per le librerie di classi portabili classi e metodi nelle applicazioni front-end. Di seguito è riportato un esempio:

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Progetti condivisi

Il tipo di progetto Asset condiviso consente di organizzare il codice sorgente in un progetto separato che recupera quindi incluso e compilato nelle singole specifiche della piattaforma front-end App e usare `#if` secondo necessità per gestire le direttive del compilatore requisiti specifici della piattaforma.

Dimensioni e della complessità della parte anteriore terminare le applicazioni per dispositivi mobili che utilizzano codice condiviso, oltre a dimensioni e la complessità del codice condiviso, è necessario essere preso in considerazione la scelta del metodo di supporto nativo dei dati dei tipi di cross-platform con di Tipo di progetto Asset condiviso.

In base a questi fattori, i seguenti tipi di soluzioni potrebbero essere implementati utilizzando il `if __UNIFIED__ ... #endif` direttive del compilatore per gestire le modifiche specifiche API unificata per il codice.

#### <a name="using-duplicate-methods"></a>Uso di metodi duplicati

Nell'esempio di una libreria che esegue trasformazioni sui dati rettangolare sopra specificati. Se la libreria contiene solo uno o due metodi molto semplici, è possibile scegliere di creare versioni duplicate di tali metodi per xamarin. IOS e xamarin. Android. Ad esempio:

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

Nel codice precedente, poiché il `CalculateArea` routine è molto semplice, abbiamo utilizzato la compilazione condizionale e creato un separato e versione dell'API unificata del metodo. D'altra parte, se la libreria contiene molte routine o diverse routine complesse, questa soluzione non sarebbe fattibile, perché potrebbero presentarsi un problema mantenendo tutti i metodi di sincronizzazione per le modifiche o correzioni di bug.

#### <a name="using-method-overloads"></a>Overload di metodo

In tal caso, potrebbe essere la soluzione per creare una versione di overload dei metodi mediante i tipi di dati a 32 bit in modo che seguano ora `CGRect` come parametro e/o valore restituito, convertire tale valore a una `RectangleF` (sapendo che la conversione da `nfloat` per `float` è una conversione con perdita di dati) e chiamare la versione originale della routine per svolgere il lavoro effettivo. Ad esempio:

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

Anche in questo caso si tratta di una buona soluzione, purché la perdita di precisione non influiscono sui risultati per esigenze specifiche dell'applicazione.

#### <a name="using-alias-directives"></a>Usando direttive Alias

Per le aree in cui la perdita di precisione è un problema, un'altra possibile soluzione consiste nell'usare `using` direttive per creare un alias per i tipi di dati nativo e CoreGraphics includendo il codice seguente all'inizio del file del codice sorgente condiviso e la conversione di qualsiasi necessità `int`, `uint` oppure `float` valori `nint`, `nuint` o `nfloat`:

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

Si noti che qui abbiamo modificato la `CalculateArea` metodo restituiscono una `nfloat` invece dello standard `float`. Tale operazione viene eseguita in modo che non si otterrebbe un errore di compilazione tentando _in modo implicito_ convertire le `nfloat` risultato del calcolo (poiché sono entrambi i valori che viene moltiplicati `nfloat`) in un `float` valore restituito.

Se il codice viene compilato ed eseguito in un dispositivo non API unificata, il `using nfloat = global::System.Single;` viene eseguito il mapping di `nfloat` a un `Single` che convertirà in modo implicito in un `float` consentendo all'applicazione front-end dispendiosa in termini di chiamare il `CalculateArea` metodo senza modifica.


#### <a name="using-type-conversions-in-the-front-end-app"></a>Usa le conversioni di tipo nell'App Front-End

Nel caso in cui le applicazioni front-end solo apportano un numero limitato di chiamate alla libreria di codice condiviso, un'altra soluzione potrebbe essere quello di lasciare la libreria non modificata e cast di tipo nell'applicazione xamarin. IOS o xamarin. Mac quando si chiama la routine esistente. Di seguito è riportato un esempio:

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Se l'applicazione del consumo offre centinaia di chiamate alla libreria di codice condiviso, anche in questo caso, potrebbe non trattarsi di una buona soluzione.

Basate sull'architettura dell'applicazione, abbiamo congestione usando uno o più delle soluzioni sopra per il supporto nativo dei dati (se richieste) i tipi nel codice multipiattaforma.


## <a name="xamarinforms-applications"></a>Xamarin.Forms Applications

Di seguito è necessario usare xamarin. Forms per le interfacce utente multipiattaforma che verranno condivise anche con un'applicazione API unificata:

- L'intera soluzione deve usare versione 1.3.1 (o versione successiva) del pacchetto NuGet xamarin. Forms.
- Per qualsiasi rendering personalizzato di xamarin. IOS, usare gli stessi tipi di soluzioni presentate nell'esempio precedente basato sul modo in cui il codice dell'interfaccia utente è stato condiviso (progetto condiviso o libreria di classi Portabile).

Come in un'applicazione multipiattaforma standard, l'esistenti a 32 bit i tipi di dati usare in qualsiasi codice condiviso e lo sviluppo multipiattaforma per la maggior parte delle situazioni tutti. Nuovi tipi di dati nativo deve essere utilizzati solo quando si effettua una chiamata a un'API iOS o Mac in cui il supporto per i tipi compatibili con architettura sono necessari.

Per altre informazioni, vedere la [l'aggiornamento di App xamarin. Forms esistente](https://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/) documentazione.

## <a name="summary"></a>Riepilogo

In questo articolo abbiamo vedere quando è necessario usare i tipi di dati nativi in un'applicazione API unificata e le implicazioni lo sviluppo multipiattaforma. Abbiamo presentato diverse soluzioni che possono essere usate in situazioni in cui è necessario usare nuovi tipi di dati nativi nelle librerie multipiattaforma. E abbiamo visto una Guida rapida per il supporto di Unified API nelle applicazioni multipiattaforma con xamarin. Forms.



## <a name="related-links"></a>Collegamenti correlati

- [API unificata](~/cross-platform/macios/unified/index.md)
- [Tipi nativi](~/cross-platform/macios/nativetypes.md)
- [Opzioni di condivisione codice](~/cross-platform/app-fundamentals/code-sharing.md)
- [Esempio di condivisione del codice](https://developer.xamarin.com/samples/mobile/SharingCode/)
