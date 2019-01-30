---
title: Sottoclassi generiche di NSObject in xamarin. IOS
description: Questo documento descrive come creare creare sottoclassi generiche di NSObject. Esamina che cosa può e non può essere eseguita, vengono illustrate le registrar statico e si esaminano le prestazioni.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 512280e9c298cfbcea6f693b0691236fd1cf5a5f
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233692"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Sottoclassi generiche di NSObject in xamarin. IOS

## <a name="using-generics-with-nsobjects"></a>Utilizzo di generics con NSObjects

A partire da xamarin. IOS 7.2.1 è possibile utilizzare i generics in sottoclassi della `NSObject` (ad esempio [UIView](xref:UIKit.UIView).

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

Poiché gli oggetti che sottoclasse `NSObject` registrati con il runtime di Objective-C sono previste alcune limitazioni su ciò che è possibile eseguire con sottoclassi generiche di `NSObject` tipi.
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considerazioni per sottoclassi generiche di NSObject

Questo documento descrive le limitazioni nel supporto limitato per sottoclassi generiche di `NSObjects` introdotte con xamarin. IOS 7.2.1.
    
### <a name="generic-type-arguments-in-member-signatures"></a>Argomenti di tipo generico nelle firme dei membri

Tutti gli argomenti di tipo generico in Objective-c esposta una firma del membro devono avere un `NSObject` vincolo.

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

**Motivo**: Il parametro di tipo generico è un `NSObject`, quindi la firma del selettore per `myMethod:` possono essere esposte in modo sicuro da Objective-C (sarà sempre `NSObject` o una sottoclasse).

**Errata**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: non è possibile creare una firma di Objective-C per i membri esportati che può chiamare codice Objective-C, poiché la firma differiscono a seconda del tipo esatto del tipo generico `T`.

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

**Motivo**: è possibile non vincolato argomenti tipo generico, purché non hanno parte della firma del membro esportato.

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

**Motivo**: il `T` parametro in Objective-C esportate `MyMethod` sarà un' `NSObject`, il tipo non vincolato `U` non fa parte della firma.
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>Creazioni di istanze di tipi generici da Objective-C

Non è consentita la creazione di istanze di tipi generici da Objective-C. Ciò si verifica in genere quando un file xib viene usato un tipo gestito.

Prendere in considerazione questa definizione di classe, che espone un costruttore che accetta un `IntPtr` (il modo di xamarin. IOS di creazione di un C# oggetto da un'istanza di Objective-C nativa):
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Mentre il costrutto sopra riportato è corretto, in fase di esecuzione, verrà generata un'eccezione in se Objective-C tenta di creare un'istanza.

Si tratta infatti Objective-C non prevede il concetto di tipi generici e non deve specificare il tipo generico esatto da creare.

È possibile trattare questo problema creando una sottoclasse specializzata del tipo generico.   Ad esempio:
    
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

A questo punto l'assenza di ambiguità più, la classe `GenericUIView` può essere usato in xib.

## <a name="no-support-for-generic-methods"></a>Nessun supporto per metodi generici

### <a name="generic-methods-are-not-allowed"></a>I metodi generici non sono consentiti.

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

**Motivo**: Ciò non è consentito perché xamarin. IOS non sa quale tipo per usare per l'argomento di tipo `T` quando il metodo viene richiamato da Objective-C.

In alternativa è possibile creare un metodo specializzato ed esportare invece che:

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

È possibile esporre un membri statici Objective-c non se è ospitato all'interno di una sottoclasse generica di `NSObject`.

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

**Motivo:** Esattamente come i metodi generici, il runtime di xamarin. IOS deve essere in grado di conoscere il tipo da utilizzare per l'argomento di tipo generico T.

Ad esempio i membri viene utilizzata l'istanza stessa (poiché non sarà mai un'istanza generica<T>, sarà sempre generico<SomeSpecificClass>), ma per i membri statici non sono presente queste informazioni.

Si noti che questo vale anche se il membro in questione non usa l'argomento di tipo T in alcun modo.

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

### <a name="requires-new-static-registrar"></a>Richiede nuovo programma di registrazione statico

Il supporto dei generics richiede le nuove [sistema di registrazione](~/ios/internals/registrar.md).

Se si prova a usare il vecchio sistema legacy di registrazione Visualizza avvisi in presenza di tipi generici (in aggiunta per non generare il codice corretto, causando un comportamento non definito).
    
## <a name="performance"></a>Prestazioni

Il programma di registrazione statico non è possibile risolvere un membro esportato in un tipo generico in fase di compilazione a quanto accade in genere, devono essere cercati in fase di esecuzione. Ciò significa che il richiamo di tale metodo da Objective-C è leggermente più lento rispetto al richiamo di membri di classi non generiche.

