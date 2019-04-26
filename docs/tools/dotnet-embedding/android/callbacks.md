---
title: Callback in Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: c6eaf4dd90b172053b4b87e3427cfe35213c6727
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61215878"
---
# <a name="callbacks-on-android"></a>Callback in Android

La chiamata a Java dal C# è più o meno un *rischiosa*. Vale a dire è disponibile un' *pattern* per i callback da C# a Java; tuttavia, è più complicato ci piacerebbe.

Illustreremo le tre opzioni per eseguire l'operazione di callback che più adatti per Java:

- Classi astratte
- Interfacce
- Metodi virtuali

## <a name="abstract-classes"></a>Classi astratte

Questa è la route più semplice per i callback, pertanto è consigliabile utilizzare `abstract` se solo si sta tentando di ottenere un callback utilizzando la forma più semplice.

Iniziamo con un C# classe vorremmo Java per implementare:

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

Ecco i dettagli per ottenere questo risultato:

- `[Register]` Genera un nome di pacchetto interessante in Java, si otterrà un nome del pacchetto generato automaticamente senza di essa.
- Creazione di una sottoclasse `Java.Lang.Object` segnali all'incorporamento di .NET per eseguire la classe tramite il generatore di Java di xamarin. Android.
- Costruttore vuoto: è ciò che è opportuno usare dal codice Java.
- `(IntPtr, JniHandleOwnership)` costruttore: che cosa verrà usato xamarin. Android per la creazione di C#-equivalente di oggetti Java.
- `[Export]` Segnala xamarin. Android per esporre il metodo a Java. È anche possibile modificare il nome del metodo, poiché tutto il mondo Java a Me piace utilizzare i metodi di lettere minuscole.

Avanti creiamo un C# metodo per testare lo scenario:

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
`JavaCallbacks` può essere qualsiasi classe per eseguire il test, purché è una `Java.Lang.Object`.

A questo punto, eseguire l'incorporamento .NET sull'assembly .NET per generare un AAR. Vedere le [Guida introduttiva](~/tools/dotnet-embedding/get-started/java/android.md) per informazioni dettagliate.

Dopo aver importato il file AAR in Android Studio, è possibile scrivere uno unit test:

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
Quindi, abbiamo:

- Implementato il `AbstractClass` in Java con un tipo anonimo
- Assicurati che l'istanza restituisce `"Java"` da Java
- Assicurati che l'istanza restituisce `"Java"` daC#
- Aggiunti `throws Throwable`, poiché C# sono attualmente contrassegnate con `throws`

Se è stato eseguito l'unit test come-è, avrà esito negativo con un errore, ad esempio:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

Cosa manca di seguito è riportato un `Invoker` tipo. Si tratta di una sottoclasse `AbstractClass` che inoltra C# chiamate a Java. Se entra in un oggetto Java la C# world e l'equivalente C# tipo è astratto e quindi xamarin. Android cerca automaticamente un C# tipo con il suffisso `Invoker` all'interno di C# codice.

Xamarin. Android Usa questo `Invoker` pattern per progetti di binding Java tra le altre cose.

Ecco la nostra implementazione di `AbstractClassInvoker`:
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

È piuttosto un bit in corso, è:

- Aggiunta di una classe con il suffisso `Invoker` che rappresenti le sottoclassi `AbstractClass`
- Aggiunti `class_ref` che include il riferimento JNI alla classe Java che rappresenti le sottoclassi nostro C# classe
- Aggiunti `id_gettext` per contenere il riferimento JNI di Java `getText` (metodo)
- Incluso un `(IntPtr, JniHandleOwnership)` costruttore
- Implementata `ThresholdType` e `ThresholdClass` come requisito per xamarin. Android per conoscere i dettagli di `Invoker`
- `GetText` necessario per effettuare la ricerca di Java `getText` metodo con la firma JNI appropriata e chiamarlo
- `Dispose` è necessaria solo per cancellare il riferimento a `class_ref`

Dopo l'aggiunta di questa classe e la generazione di un nuovo AAR, nostro unit test passa. Come si può notare questo modello per i callback non è *ideale*, ma fattibile.

Per informazioni dettagliate sull'interoperabilità Java, vedere le straordinarie [documentazione di xamarin. Android](~/android/platform/java-integration/working-with-jni.md) su questo argomento.

## <a name="interfaces"></a>Interfacce

Le interfacce sono molto simile alle classi astratte, ad eccezione di un dettaglio: Xamarin. Android non genera Java per loro. Infatti, prima dell'incorporamento di .NET, non esistono molti scenari in cui è necessario implementare Java un C# interfaccia.

Si supponga di avere il seguente C# interfaccia:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` segnala all'incorporamento di .NET che si tratta di un'interfaccia xamarin. Android, ma in caso contrario, questo è esattamente identico un `abstract` classe.

Poiché xamarin. Android attualmente non genera il codice Java per questa interfaccia, aggiungere il Java seguente per il C# progetto:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

È possibile inserire il file in un punto qualsiasi, ma assicurarsi di impostare la relativa azione di compilazione `AndroidJavaSource`. Ciò indicherà .NET l'incorporamento per copiarlo nella directory appropriata per vengono compilati nel file AAR.

Successivamente, il `Invoker` implementazione sarà pressoché uguali:

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

Dopo aver generato un file AAR, test in Android Studio, potremmo creare l'unità di passaggio seguente:

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

Viene sottoposto a override un `virtual` in Java è possibile, ma non un'esperienza ottimale.

Si supponga di disporre dei seguenti C# classe:

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

Se è stata seguita la `abstract` esempio classe precedente, potrebbe essere adatto ad eccezione di un dettaglio: _Xamarin. Android non cercare le `Invoker`_ .

Per risolvere questo problema, modificare il C# classe venga `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Questo non è ideale, ma ottiene in questo scenario. Xamarin. Android selezionerà il `VirtualClassInvoker` e può usare Java `@Override` sul metodo.

## <a name="callbacks-in-the-future"></a>Callback in futuro

Esistono un paio di cose che è possibile migliorare questi scenari:

1. `throws Throwable` in C# costruttori viene risolto in questa [richiesta pull](https://github.com/xamarin/java.interop/pull/170).
1. Rendere il generatore di Java in xamarin. Android supporta le interfacce.
    - Ciò elimina la necessità per l'aggiunta di file di origine Java con un'azione di compilazione `AndroidJavaSource`.
1. Rendere un modo per xamarin. Android caricare un `Invoker` delle classi virtuali.
    - Ciò elimina la necessità di contrassegnare la classe nel nostro `virtual` esempio `abstract`.
1. Generare `Invoker` automaticamente le classi per l'incorporamento di .NET
    - Questa dovrà essere complicata, ma fattibile. Xamarin. Android sta già eseguendo un'operazione simile al seguente per progetti di binding Java.

C'è molto del lavoro da eseguire qui, ma tali miglioramenti all'incorporamento di .NET sono possibili.

## <a name="further-reading"></a>Ulteriori informazioni

* [Introduzione all'uso su Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Ricerca preliminare Android](~/tools/dotnet-embedding/android/index.md)
* [Limitazioni di incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
* [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Le descrizioni e i codici di errore](~/tools/dotnet-embedding/errors.md)
