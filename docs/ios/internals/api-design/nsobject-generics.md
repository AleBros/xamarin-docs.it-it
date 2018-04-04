---
title: Sottoclassi generiche di NSObject
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 89df751d74b9b54ae8138d2e1b24c61d82c3cac8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="generic-subclasses-of-nsobject"></a>Sottoclassi generiche di NSObject

## <a name="using-generics-with-nsobjects"></a>Utilizzo di generics con NSObjects

A partire da xamarin 7.2.1 è possibile utilizzare generics in sottoclassi della `NSObject` (ad esempio [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)).

È ora possibile creare le classi generiche, simile alla seguente:

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Poiché gli oggetti che sottoclasse `NSObject` sono registrati con il runtime Objective-C sono previste alcune limitazioni su ciò che è possibile con le sottoclassi generiche di `NSObject` tipi.
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considerazioni per le sottoclassi generiche di NSObject

Questo documento illustra in dettaglio le limitazioni il supporto limitato per le sottoclassi generiche di `NSObjects` introdotte con xamarin. IOS 7.2.1.
    
### <a name="generic-type-arguments-in-member-signatures"></a>Argomenti di tipo generici nelle firme dei membri

Tutti gli argomenti di tipo generico in una firma del membro esposti a Objective-C devono avere un `NSObject` vincolo.

**Buona**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: il parametro di tipo generico è un `NSObject`, pertanto la firma del selettore per `myMethod:` possano essere esposti in modo sicuro a Objective-C (sarà sempre `NSObject` o una sottoclasse).

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

**Motivo**: non è possibile creare una firma di Objective-C per i membri esportati in grado di chiamare codice Objective-C, poiché la firma potrebbe variare a seconda del tipo esatto di tipo generico `T`.

**Buona**:

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

**Motivo**: è possibile non vincolato argomenti di tipo generico, purché non accettano parte della firma di un membro esportato.

**Buona**:

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

**Motivo**: il `T` parametro in Objective-C esportato `MyMethod` deve essere un `NSObject`, il tipo senza vincolato `U` non fa parte della firma.
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>Creazioni di istanze di tipi generici da Objective-C

Non è consentita la creazione di istanze di tipi generici da Objective-C. Ciò si verifica in genere quando un tipo gestito viene utilizzato in un XI.

Prendere in considerazione la definizione di classe, che espone un costruttore che accetta un `IntPtr` (il modo di xamarin. IOS di costruzione di un oggetto c# da un'istanza di Objective-C nativa):
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Mentre il costrutto precedente è un problema, in fase di esecuzione, verrà generata un'eccezione in se Objective-C tenta di creare un'istanza.

Si tratta infatti Objective-C non ha alcun concetto di tipi generici e non è possibile specificare il tipo esatto generico per creare.

Questo problema può essere aggirato mediante la creazione di una sottoclasse specializzata del tipo generico.   Ad esempio:
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

Ora l'assenza di ambiguità più, la classe `GenericUIView` può essere usato in xibs.

## <a name="no-support-for-generic-methods"></a>Nessun supporto per i metodi generici

### <a name="generic-methods-are-not-allowed"></a>Metodi generici non sono consentiti.

Non verrà compilato il codice seguente:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**Motivo**: ciò non è consentito poiché xamarin. IOS non conosce il tipo da utilizzare per l'argomento di tipo `T` quando viene richiamato il metodo da Objective-C.

In alternativa è possibile creare un metodo specializzato ed esportare che invece:

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

### <a name="no-exported-static-members-allowed"></a>Nessun membro statico esportato consentito

È possibile esporre un membri statici per Objective-C non se è ospitato all'interno di una sottoclasse generica `NSObject`.

Esempio di uno scenario non supportato:

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

**Motivo:** esattamente come i metodi generici, la fase di esecuzione di xamarin. IOS deve essere in grado di conoscere il tipo da utilizzare per l'argomento di tipo generico T.

I membri per l'istanza viene utilizzata l'istanza stessa (poiché non sarà mai un'istanza generica<T>, sarà sempre generico<SomeSpecificClass>), ma per i membri statici non sono presente queste informazioni.

Si noti che questo si applica anche se il membro in questione non utilizza l'argomento di tipo T in alcun modo.

L'alternativa in questo caso consiste nella creazione di una sottoclasse specializzata:

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIVIewProperty {
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

### <a name="requires-new-static-registrar"></a>Richiede una nuova registrazione statico

Il supporto dei generics richiede il nuovo [sistema di registrazione](~/ios/internals/registrar.md).

Se si tenta di usare il precedente sistema legacy di registrazione visualizzerà gli avvisi in presenza di tipi generici (in aggiunta per non generare il codice corretto, determinando un comportamento non definito).
    
## <a name="performance"></a>Prestazioni

Il programma di registrazione statico non è possibile risolvere un membro esportato in un tipo generico in fase di compilazione come al solito, è possibile cercare in fase di esecuzione. Ciò significa che la chiamata di tale metodo da Objective-C è leggermente più lenta rispetto al richiamo di membri di classi non generiche.

