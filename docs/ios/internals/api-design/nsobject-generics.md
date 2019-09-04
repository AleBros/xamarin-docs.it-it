---
title: Sottoclassi generiche di NSObject in Novell. iOS
description: Questo documento descrive come creare sottoclassi generiche di NSObject. Esamina le operazioni che possono e non possono essere eseguite, illustra il registrar statico e osserva le prestazioni.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 2e84ec85fac933f579f961300d242bafa1f0b838
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226044"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Sottoclassi generiche di NSObject in Novell. iOS

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

Poiché gli oggetti sottoclass `NSObject` sono registrati con il runtime di Objective-C, esistono alcune limitazioni rispetto a quanto possibile con le sottoclassi generiche dei `NSObject` tipi.

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considerazioni sulle sottoclassi generiche di NSObject

Questo documento illustra in dettaglio le limitazioni del supporto limitato per le sottoclassi generiche di `NSObjects`.

### <a name="generic-type-arguments-in-member-signatures"></a>Argomenti di tipo generico nelle firme di membro

Tutti gli argomenti di tipo generico in una firma del membro esposti a Objective-C `NSObject` devono avere un vincolo.

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

**Motivo**: Il parametro di tipo generico è `NSObject`un oggetto, pertanto la firma `myMethod:` del selettore per può essere esposta in modo sicuro a Objective `NSObject` -C (sarà sempre o una sottoclasse).

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

**Motivo**: non è possibile creare una firma Objective-c per i membri esportati che il codice Objective-c può chiamare, perché la firma varia a seconda del tipo esatto del tipo `T`generico.

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

**Motivo**: il `T` parametro in Objective-C esportato `MyMethod` è vincolato a un oggetto `NSObject`, il tipo `U` non vincolato non fa parte della firma.

### <a name="instantiations-of-generic-types-from-objective-c"></a>Creazioni di istanze di tipi generici da Objective-C

La creazione di istanze di tipi generici da Objective-C non è consentita. Questo si verifica in genere quando un tipo gestito viene usato in un XIB o in uno storyboard.

Si consideri questa definizione di classe, che espone un `IntPtr` costruttore che accetta un (il metodo Novell. iOS C# per costruire un oggetto da un'istanza nativa di Objective-C):

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Mentre il costrutto precedente è corretto, in fase di esecuzione verrà generata un'eccezione in caso in cui Objective-C tenti di crearne un'istanza.

Questa situazione si verifica perché Objective-C non ha alcun concetto di tipi generici e non può specificare il tipo generico esatto da creare.

Questo problema può essere risolto creando una sottoclasse specializzata del tipo generico. Ad esempio:

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

**Motivo**: Questa operazione non è consentita perché Novell. iOS non conosce il tipo da usare per l' `T` argomento tipo quando il metodo viene richiamato da Objective-C.

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

Non è possibile esporre membri statici a Objective-C se sono ospitati all'interno di una sottoclasse `NSObject`generica di.

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

**Motivo** Proprio come per i metodi generici, il runtime di Novell. iOS deve essere in grado di conoscere il tipo da usare per `T`l'argomento di tipo generico.

Per i membri di istanza viene utilizzata l'istanza stessa (poiché non vi sarà mai `Generic<T>`un'istanza, sarà `Generic<SomeSpecificClass>`sempre), ma per i membri statici queste informazioni non sono presenti.

Si noti che questo vale anche se il membro in questione non usa in alcun modo `T` l'argomento di tipo.

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

