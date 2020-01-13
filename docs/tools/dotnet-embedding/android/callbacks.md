---
title: Callback in Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: f23f155a02422a3d04a0b14b282929ea63d60765
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007295"
---
# <a name="callbacks-on-android"></a>Callback in Android

La chiamata a Java C# da è un' *attività piuttosto rischiosa*. Ovvero è disponibile un *modello* per i callback da C# a Java; Tuttavia, è più complesso di quanto si desideri.

Verranno illustrate le tre opzioni per eseguire le richiamate che risultano più sensate per Java:

- Classi astratte
- Interfacce
- Metodi virtuali

## <a name="abstract-classes"></a>classi astratte

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

- `[Register]` genera un nome di pacchetto piacevole in Java. si otterrà un nome di pacchetto generato automaticamente senza di esso.
- La sottoclasse `Java.Lang.Object` segnala l'incorporamento di .NET per eseguire la classe tramite il generatore Java Xamarin.Android.
- Costruttore vuoto: è quello che si intende usare dal codice Java.
- `(IntPtr, JniHandleOwnership)` Costruttore: è ciò che Xamarin.Android userà per la creazione C#dell'equivalente degli oggetti Java.
- `[Export]` segnala a Xamarin.Android di esporre il metodo a Java. È anche possibile modificare il nome del metodo, poiché il mondo Java mi piace usare i metodi minuscoli.

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

`JavaCallbacks` può essere una classe per testarla, purché sia una `Java.Lang.Object`.

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

- Implementazione del `AbstractClass` in Java con un tipo anonimo
- Assicurarsi che l'istanza restituisca `"Java"` da Java
- Assicurarsi che l'istanza restituisca `"Java"` daC#
- Aggiunta `throws Throwable`, poiché C# i costruttori sono attualmente contrassegnati con `throws`

Se questo unit test è stato eseguito così com'è, l'errore potrebbe essere simile al seguente:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

Ciò che manca qui è un tipo di `Invoker`. Si tratta di una sottoclasse di `AbstractClass` che C# Invia le chiamate a Java. Se un oggetto Java entra nel C# mondo e il tipo C# equivalente è abstract, Xamarin.Android cerca automaticamente un C# tipo con il suffisso`Invoker`per l'uso all' C# interno del codice.

Xamarin.Android usa questo modello di `Invoker` per i progetti di associazione Java tra le altre cose.

Di seguito è illustrata l'implementazione di `AbstractClassInvoker`:

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

- Aggiunta di una classe con il suffisso `Invoker` le sottoclassi `AbstractClass`
- Aggiunta `class_ref` per conservare il riferimento JNI alla classe Java che sottoclassi la C# classe
- Aggiunta `id_gettext` per conservare il riferimento JNI al metodo Java `getText`
- È stato incluso un costruttore `(IntPtr, JniHandleOwnership)`
- Implementato `ThresholdType` e `ThresholdClass` come requisito per Xamarin.Android per conoscere i dettagli relativi al `Invoker`
- `GetText` necessario per cercare il metodo Java `getText` con la firma JNI appropriata e chiamarlo
- `Dispose` è necessario solo per cancellare il riferimento a `class_ref`

Dopo l'aggiunta di questa classe e la generazione di un nuovo AAR, il unit test passerà. Come si può notare, questo modello per le richiamate non è *ideale*, ma fattibile.

Per informazioni dettagliate sull'interoperabilità Java, vedere la documentazione straordinaria di [Xamarin.Android](~/android/platform/java-integration/working-with-jni.md) in questo argomento.

## <a name="interfaces"></a>Interfacce

Le interfacce sono molto le stesse delle classi astratte, ad eccezione di un dettaglio: Xamarin.Android non genera Java per questi elementi. Questo è dovuto al fatto che prima dell'incorporamento di .NET non ci sono molti scenari in C# cui Java implementa un'interfaccia.

Supponiamo che sia presente l'interfaccia C# seguente:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` segnala all'incorporamento di .NET che si tratta di un'interfaccia Xamarin.Android, ma in caso contrario è esattamente uguale a una classe `abstract`.

Poiché Xamarin.Android non genera attualmente il codice Java per questa interfaccia, aggiungere il codice Java seguente al C# progetto:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

È possibile inserire il file in un punto qualsiasi, ma assicurarsi di impostare l'azione di compilazione su `AndroidJavaSource`. In questo modo verrà segnalato l'incorporamento di .NET per la copia nella directory corretta per essere compilato nel file AAR.

Successivamente, l'implementazione di `Invoker` sarà piuttosto identica:

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

L'override di un `virtual` in Java è possibile, ma non è un'esperienza eccezionale.

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

Se è stata seguita la classe `abstract` esempio precedente, il funzionamento è a eccezione di un dettaglio: _Xamarin.Android non esegue la ricerca del `Invoker`_.

Per risolvere questo problema, modificare C# la classe in modo che sia`abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Questo scenario non è ideale, ma si tratta di uno scenario funzionante. Xamarin.Android preleverà il `VirtualClassInvoker` e Java potrà usare `@Override` sul metodo.

## <a name="callbacks-in-the-future"></a>Callback in futuro

Ci sono un paio di cose che è possibile fare per migliorare questi scenari:

1. `throws Throwable` sui C# costruttori è stato corretto [in questa richiesta](https://github.com/xamarin/java.interop/pull/170)pull.
1. Rendere il generatore Java nelle interfacce di supporto Xamarin.Android.
    - In questo modo si elimina la necessità di aggiungere un file di origine Java con un'azione di compilazione di `AndroidJavaSource`.
1. Fare in modo che Xamarin.Android carichi un `Invoker` per le classi virtuali.
    - In questo modo si elimina la necessità di contrassegnare la classe nell'esempio `virtual` `abstract`.
1. Genera automaticamente classi di `Invoker` per l'incorporamento di .NET
    - Questa operazione sarà complicata, ma fattibile. Xamarin.Android sta già eseguendo un'operazione simile a questa per i progetti di associazione Java.

Qui è necessario eseguire numerose operazioni, ma questi miglioramenti all'incorporamento di .NET sono possibili.

## <a name="further-reading"></a>Ulteriori informazioni

- [Introduzione in Android](~/tools/dotnet-embedding/get-started/java/android.md)
- [Ricerca preliminare per Android](~/tools/dotnet-embedding/android/index.md)
- [Limitazioni dell'incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
- [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
- [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
