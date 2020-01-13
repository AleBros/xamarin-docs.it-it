---
title: Sottoclassi generiche di NSObject in Xamarin.iOS
description: Questo documento descrive come creare sottoclassi generiche di NSObject. Esamina le operazioni che possono e non possono essere eseguite, illustra il registrar statico e osserva le prestazioni.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 279fcac1611038613bf442e1b766fda45dd5a429
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022370"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Sottoclassi generiche di NSObject in Xamarin.iOS

## <a name="using-generics-with-nsobjects"></a>Uso dei generics con NSObjects

È possibile usare i generics nelle sottoclassi di `NSObject`, ad esempio [UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Poiché gli oggetti sottoclass `NSObject` vengono registrati con il runtime di Objective-C, esistono alcune limitazioni per quanto possibile con le sottoclassi generiche di `NSObject` tipi.

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considerazioni sulle sottoclassi generiche di NSObject

Questo documento illustra in dettaglio le limitazioni del supporto limitato per le sottoclassi generiche di `NSObjects`.

### <a name="generic-type-arguments-in-member-signatures"></a>Argomenti di tipo generico nelle firme di membro

Tutti gli argomenti di tipo generico in una firma del membro esposta a Objective-C devono avere un vincolo `NSObject`.

**Valido**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: il parametro di tipo generico è un `NSObject`, quindi la firma del selettore per `myMethod:` può essere esposta in modo sicuro a Objective-C (sarà sempre `NSObject` o una sottoclasse).

Non **valido**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: non è possibile creare una firma Objective-c per i membri esportati che il codice Objective-c può chiamare, perché la firma differisce a seconda del tipo esatto del tipo generico `T`.

**Valido**:

```csharp
class Generic<T> : NSObject
{
    T storage;

    [Export ("myMethod:")]
    public void MyMethod (NSObject value)
    {
    }
}
```

**Motivo**: è possibile avere argomenti di tipo generico non vincolati, purché non facciano parte della firma del membro esportato.

**Valido**:

```csharp
class Generic<T, U> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
        Console.WriteLine (typeof (U));
    }
}
```

**Motivo**: il `T` parametro nel `MyMethod` esportato Objective-C è vincolato a essere un `NSObject`, il tipo non vincolato `U` non fa parte della firma.

### <a name="instantiations-of-generic-types-from-objective-c"></a>Creazioni di istanze di tipi generici da Objective-C

La creazione di istanze di tipi generici da Objective-C non è consentita. Questo si verifica in genere quando un tipo gestito viene usato in un XIB o in uno storyboard.

Si consideri questa definizione di classe, che espone un costruttore che accetta un `IntPtr` (il metodo Xamarin.iOS C# per costruire un oggetto da un'istanza di Objective-C nativa):

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Mentre il costrutto precedente è corretto, in fase di esecuzione verrà generata un'eccezione in caso in cui Objective-C tenti di crearne un'istanza.

Questa situazione si verifica perché Objective-C non ha alcun concetto di tipi generici e non può specificare il tipo generico esatto da creare.

Questo problema può essere risolto creando una sottoclasse specializzata del tipo generico. Esempio:

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

Ora non esiste più alcuna ambiguità, la classe `GenericUIView` può essere usata in XIB o Storyboard.

## <a name="no-support-for-generic-methods"></a>Nessun supporto per i metodi generici

### <a name="generic-methods-are-not-allowed"></a>I metodi generici non sono consentiti.

Il codice seguente non verrà compilato:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**Motivo**: questa operazione non è consentita perché Xamarin.iOS non conosce il tipo da usare per l'argomento di tipo `T` quando il metodo viene richiamato da Objective-C.

In alternativa, è possibile creare un metodo specializzato ed esportare il metodo:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyUIViewMethod (UIView argument)
    {
        MyMethod<UIView> (argument);
    }
    public void MyMethod<T> (T argument)
    {
    }
}
```

### <a name="no-exported-static-members-allowed"></a>Non sono consentiti membri statici esportati

Non è possibile esporre membri statici a Objective-C se sono ospitati all'interno di una sottoclasse generica di `NSObject`.

Esempio di scenario non supportato:

```csharp
class Generic<T> : NSObject where T : NSObject
{
    [Export ("myMethod:")]
    public static void MyMethod ()
    {
    }

    [Export ("myProperty")]
    public static T MyProperty { get; set; }
}
```

**Motivo:** Proprio come per i metodi generici, il runtime di Xamarin.iOS deve essere in grado di conoscere il tipo da usare per l'argomento di tipo generico `T`.

Per i membri di istanza viene utilizzata l'istanza stessa (poiché non ci sarà mai un'istanza `Generic<T>`, sarà sempre `Generic<SomeSpecificClass>`), ma per i membri statici queste informazioni non sono presenti.

Si noti che questo vale anche se il membro in questione non usa l'argomento tipo `T` in alcun modo.

L'alternativa in questo caso consiste nel creare una sottoclasse specializzata:

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIViewProperty {
        get { return MyProperty; }
        set { MyProperty = value; }
    }
}

class Generic<T> : NSObject where T : NSObject
{
    public static void MyMethod () {}
    public static T MyProperty { get; set; }
}
```

## <a name="performance"></a>Prestazioni

Il registrar statico non riesce a risolvere un membro esportato in un tipo generico in fase di compilazione, come in genere, deve essere ricercato in fase di esecuzione. Ciò significa che richiamare un metodo di questo tipo da Objective-C è leggermente più lento rispetto alla chiamata di membri da classi non generiche.
