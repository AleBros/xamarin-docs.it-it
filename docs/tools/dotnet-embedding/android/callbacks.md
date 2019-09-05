---
title: Callback in Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: bfe12d84510707ff6e81aae2b5b20be7e9cacd59
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293059"
---
# <a name="callbacks-on-android"></a>Callback in Android

La chiamata a Java C# da è un' *attività piuttosto rischiosa*. Ovvero è disponibile un *modello* per i callback da C# a Java; Tuttavia, è più complesso di quanto si desideri.

Verranno illustrate le tre opzioni per eseguire le richiamate che risultano più sensate per Java:

- Classi astratte
- Interfacce
- Metodi virtuali

## <a name="abstract-classes"></a>Classi astratte

Si tratta del percorso più semplice per i callback, quindi è consigliabile usare `abstract` se si sta tentando di ottenere un callback funzionante nella forma più semplice.

Si inizierà con una C# classe che verrà implementata da Java:

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

Ecco i dettagli per eseguire questa operazione:

- `[Register]`genera un nome di pacchetto piacevole in Java. si otterrà un nome di pacchetto generato automaticamente senza di esso.
- La sottoclasse `Java.Lang.Object` segnala l'incorporamento di .NET per eseguire la classe tramite il generatore Java Novell. Android.
- Costruttore vuoto: è quello che si intende usare dal codice Java.
- `(IntPtr, JniHandleOwnership)`Constructor: è quello che Novell. Android userà per la creazione C#dell'equivalente degli oggetti Java.
- `[Export]`segnala a Novell. Android di esporre il metodo a Java. È anche possibile modificare il nome del metodo, poiché il mondo Java mi piace usare i metodi minuscoli.

Successivamente, si crea un C# metodo per testare lo scenario:

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```

`JavaCallbacks`può essere una classe qualsiasi per testarla, purché sia un oggetto `Java.Lang.Object`.

A questo punto, eseguire l'incorporamento .NET nell'assembly .NET per generare un AAR. Per informazioni dettagliate, vedere la [guida introduzione](~/tools/dotnet-embedding/get-started/java/android.md) .

Dopo aver importato il file AAR in Android Studio, è ora di scrivere un unit test:

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```

Quindi:

- `AbstractClass` Implementato in Java con un tipo anonimo
- Verificare che l'istanza sia `"Java"` stata restituita da Java
- Verificare che l'istanza venga `"Java"` restituita daC#
- Aggiunto `throws Throwable`, perché C# i costruttori sono attualmente contrassegnati con`throws`

Se questo unit test è stato eseguito così com'è, l'errore potrebbe essere simile al seguente:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

Ciò che manca qui è un `Invoker` tipo. Si tratta di una sottoclasse di `AbstractClass` che C# Invia le chiamate a Java. Se un oggetto Java entra nel C# mondo e il tipo C# equivalente è abstract, Novell. Android cerca automaticamente un C# tipo con il suffisso `Invoker` da usare all'interno C# del codice.

Novell. Android usa questo `Invoker` modello per i progetti di associazione Java tra le altre cose.

Ecco l'implementazione di `AbstractClassInvoker`:

```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

C'è molto da fare qui:

- Aggiunta di una classe con il `Invoker` suffisso che sottoclassi`AbstractClass`
- Aggiunto `class_ref` per conservare il riferimento JNI alla classe Java che sottoclassa la C# classe
- Aggiunto `id_gettext` per conservare il riferimento JNI al metodo Java `getText`
- Un `(IntPtr, JniHandleOwnership)` Costruttore incluso
- Implementato `ThresholdType` e`ThresholdClass` come requisito per Novell. Android per conoscere i dettagli relativi al`Invoker`
- `GetText`necessaria per cercare il metodo `getText` Java con la firma JNI appropriata e chiamarlo
- `Dispose`è necessario solo per cancellare il riferimento a`class_ref`

Dopo l'aggiunta di questa classe e la generazione di un nuovo AAR, il unit test passerà. Come si può notare, questo modello per le richiamate non è *ideale*, ma fattibile.

Per informazioni dettagliate sull'interoperabilità Java, vedere la documentazione straordinaria di [Novell. Android](~/android/platform/java-integration/working-with-jni.md) in questo argomento.

## <a name="interfaces"></a>Interfacce

Le interfacce sono molto le stesse delle classi astratte, ad eccezione di un dettaglio: Novell. Android non genera Java per tali elementi. Questo è dovuto al fatto che prima dell'incorporamento di .NET non ci sono molti scenari in C# cui Java implementa un'interfaccia.

Supponiamo che sia presente l'interfaccia C# seguente:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject`segnala all'incorporamento di .NET che si tratta di un'interfaccia Novell. Android, ma in caso contrario è esattamente uguale `abstract` a una classe.

Poiché Novell. Android non genera attualmente il codice Java per questa interfaccia, aggiungere il codice Java seguente al C# progetto:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

È possibile inserire il file in un punto qualsiasi, ma assicurarsi di impostare l'azione `AndroidJavaSource`di compilazione su. In questo modo verrà segnalato l'incorporamento di .NET per la copia nella directory corretta per essere compilato nel file AAR.

Successivamente, l' `Invoker` implementazione sarà piuttosto identica:

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

Dopo aver generato un file AAR, in Android Studio è possibile scrivere il passaggio seguente unit test:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>Metodi virtuali

L'override `virtual` di in Java è possibile, ma non è un'esperienza eccezionale.

Si supponga di avere la classe seguente C# :

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

Se è stata seguita `abstract` l'esempio precedente della classe, il funzionamento è a eccezione di un dettaglio: _Novell. Android non eseguirà `Invoker`la ricerca di_ .

Per risolvere il problema, modificare C# la classe in `abstract`modo che sia:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Questo scenario non è ideale, ma si tratta di uno scenario funzionante. Novell. Android preleverà `VirtualClassInvoker` e Java potrà usare `@Override` sul metodo.

## <a name="callbacks-in-the-future"></a>Callback in futuro

Ci sono un paio di cose che è possibile fare per migliorare questi scenari:

1. `throws Throwable`on C# i costruttori sono corretti [in questa richiesta](https://github.com/xamarin/java.interop/pull/170)pull.
1. Rendere il generatore Java nelle interfacce di supporto Novell. Android.
    - In questo modo si elimina la necessità di aggiungere il file di origine Java `AndroidJavaSource`con un'azione di compilazione di.
1. Fare in modo che Novell. Android carichi un `Invoker` per le classi virtuali.
    - In questo modo si elimina la necessità di contrassegnare `virtual` la `abstract`classe nell'esempio.
1. Genera `Invoker` automaticamente classi per l'incorporamento di .NET
    - Questa operazione sarà complicata, ma fattibile. Novell. Android sta già eseguendo un'operazione simile a questa per i progetti di associazione Java.

Qui è necessario eseguire numerose operazioni, ma questi miglioramenti all'incorporamento di .NET sono possibili.

## <a name="further-reading"></a>Ulteriori informazioni

- [Introduzione in Android](~/tools/dotnet-embedding/get-started/java/android.md)
- [Ricerca preliminare per Android](~/tools/dotnet-embedding/android/index.md)
- [Limitazioni dell'incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
- [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
- [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
