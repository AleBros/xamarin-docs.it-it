---
title: Sottoclassi generiche di NSObject in Xamarin.iOS
description: In questo documento viene descritto come creare creare sottoclassi generiche di NSObject.This document describes how to create generic subclasses of NSObject. Esamina cosa può e non può essere fatto, discute il registrar statico e prende un'occhiata alle prestazioni.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 279fcac1611038613bf442e1b766fda45dd5a429
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022370"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Sottoclassi generiche di NSObject in Xamarin.iOS

## <a name="using-generics-with-nsobjects"></a>Utilizzo di generics con NSObjectsUsing generics with NSObjects

È possibile utilizzare i generics nelle `NSObject`sottoclassi di , ad esempio [UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Poiché gli `NSObject` oggetti che fanno parte della sottoclasse sono registrati con il runtime `NSObject` Objective-C, esistono alcune limitazioni su ciò che è possibile con le sottoclassi generiche dei tipi.

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considerazioni per le sottoclassi generiche di NSObjectConsiderations for generic subclasses of NSObject

Questo documento descrive in dettaglio le limitazioni `NSObjects`del supporto limitato per le sottoclassi generiche di .

### <a name="generic-type-arguments-in-member-signatures"></a>Argomenti di tipo generico nelle firme dei membriGeneric Type Arguments in Member Signatures

Tutti gli argomenti di tipo generico in una `NSObject` firma del membro esposta a Objective-C devono avere un vincolo.

**Buono**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: Il parametro di tipo generico è un `NSObject`oggetto , pertanto la firma del selettore per `myMethod:` può essere esposta in modo sicuro a Objective-C (sarà sempre `NSObject` o una sottoclasse di esso).

**Non valido**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: non è possibile creare una firma Objective-C per i membri esportati che il codice Objective-C `T`può chiamare, poiché la firma sarebbe diversa a seconda del tipo esatto del tipo generico.

**Buono**:

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

**Buono**:

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

**Motivo** `T` : il parametro nell'esportazione Objective-C `MyMethod` è vincolato a essere un `NSObject`, il tipo `U` non vincolato non fa parte della firma.

### <a name="instantiations-of-generic-types-from-objective-c"></a>Creazione di istanze di tipi generici da Objective-C

La creazione di istanze di tipi generici da Objective-C non è consentita. Ciò si verifica in genere quando un tipo gestito viene utilizzato in uno xib o uno storyboard.

Si consideri questa definizione di classe, che espone un costruttore che accetta un (il modo Xamarin.iOS di costruire un oggetto c'è da un'istanza di Objective-C nativo):Consider this class definition, which exposes a constructor that takes an `IntPtr` (the Xamarin.iOS way of constructing a C' object from a native Objective-C instance):

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Mentre il costrutto precedente va bene, in fase di esecuzione, questo genererà un'eccezione se Objective-C tenta di creare un'istanza di esso.

Ciò accade perché Objective-C non ha alcun concetto di tipi generici e non può specificare il tipo generico esatto da creare.

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

Ora non c'è più ambiguità, la classe `GenericUIView` può essere utilizzata in xibs o storyboard.

## <a name="no-support-for-generic-methods"></a>Nessun supporto per i metodi generici

### <a name="generic-methods-are-not-allowed"></a>I metodi generici non sono consentiti.

Il codice seguente non verrà compilato:The following code will not compile:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**Motivo**: questa operazione non è consentita perché Xamarin.iOS `T` non conosce il tipo da utilizzare per l'argomento di tipo quando il metodo viene richiamato da Objective-C .

Un'alternativa consiste nel creare un metodo specializzato ed esportare che invece:An alternative is to create a specialized method and export that instead:

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

Non è possibile esporre un membro statico a Objective-C se `NSObject`è ospitato all'interno di una sottoclasse generica di .

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

**Motivo:** Analogamente ai metodi generici, il runtime di Xamarin.iOS deve `T`essere in grado di sapere quale tipo utilizzare per l'argomento di tipo generico.

Per i membri di istanza viene utilizzata l'istanza stessa (poiché non sarà mai un'istanza `Generic<T>`, sarà `Generic<SomeSpecificClass>`sempre ), ma per i membri statici queste informazioni non sono presenti.

Si noti che ciò vale anche se `T` il membro in questione non utilizza in alcun modo l'argomento type.

L'alternativa in questo caso consiste nel creare una sottoclasse specializzata:The alternative in this case is to create a specialized subclass:

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

Il registrar statico non è in grado di risolvere un membro esportato in un tipo generico in fase di compilazione come in genere, deve essere esaminato in fase di esecuzione. Ciò significa che la chiamata di tale metodo da Objective-C è leggermente più lenta rispetto alla chiamata di membri da classi non generiche.
