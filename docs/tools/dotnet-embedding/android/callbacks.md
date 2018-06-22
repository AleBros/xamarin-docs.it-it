---
title: Callback in Android
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 3f18516643c00dc67fe533ecab00e1f415eb5c46
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922822"
---
# <a name="callbacks-on-android"></a>Callback in Android

La chiamata a Java da c# è più o meno un *rischiosa*. Vale a dire è un *modello* per i callback di c# per Java; tuttavia, è più complesso desideriamo.

Verranno descritte le tre opzioni per l'esecuzione di callback che rendono più appropriate per Java:

- Classi astratte
- Interfacce
- Metodi virtuali

## <a name="abstract-classes"></a>Classi astratte

Questa è la route più semplice per i callback, pertanto è consigliabile utilizzare `abstract` se si sta solo tentando di ottenere un callback che utilizza la forma più semplice.

Iniziamo con una classe c# che desideriamo Java per implementare:

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

Di seguito sono riportati i dettagli per ottenere questo risultato:

- `[Register]` Genera un nome di pacchetto nice in Java, si otterrà un nome di pacchetto generato automaticamente senza di esso.
- Creazione di sottoclassi `Java.Lang.Object` segnali per l'incorporamento .NET per eseguire la classe tramite il generatore di Java di xamarin. Android.
- Costruttore vuoto: è ciò che è possibile utilizzare dal codice Java.
- `(IntPtr, JniHandleOwnership)` costruttore: è xamarin ciò che verrà utilizzata per la creazione di c#-equivalente di oggetti Java.
- `[Export]` Segnala xamarin per esporre il metodo per Java. È anche possibile modificare il nome del metodo, poiché tutto il mondo Java piace usare i metodi di lettere minuscole.

Avanti verifichiamo un metodo c# per lo scenario di test:

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
`JavaCallbacks` potrebbe essere di qualsiasi classe per eseguire il test, purché si tratta di un `Java.Lang.Object`.

A questo punto, eseguire .NET incorporamento sull'assembly .NET per generare un AAR. Vedere il [Guida introduttiva](~/tools/dotnet-embedding/get-started/java/android.md) per informazioni dettagliate.

Dopo avere importato il file AAR in Android Studio, è possibile scrivere uno unit test:

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
Pertanto è:

- Implementato il `AbstractClass` Java con un tipo anonimo
- Assicurarsi che l'istanza restituisce `"Java"` da Java
- Assicurarsi che l'istanza restituisce `"Java"` da c#
- Aggiunto `throws Throwable`, dal momento che in c# sono attualmente contrassegnate con `throws`

Se si è verificato questo unit test come-è, potrebbe verificarsi un errore, ad esempio:

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

Cosa manca Ecco un `Invoker` tipo. Si tratta di una sottoclasse di `AbstractClass` che inoltra le chiamate di c# per Java. Se un oggetto Java immette world c# il tipo c# equivalente è astratto, quindi xamarin cerca automaticamente un tipo c# con il suffisso `Invoker` da utilizzare all'interno di codice c#.

Usa xamarin `Invoker` modello per i progetti di binding di linguaggio tra l'altro.

Di seguito è l'implementazione Microsoft di `AbstractClassInvoker`:
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

Vi è una netta questo proposito è:

- Aggiunta di una classe con il suffisso `Invoker` che rappresenta una sottoclasse `AbstractClass`
- Aggiunto `class_ref` per contenere il JNI fare riferimento alla classe Java che rappresenta una sottoclasse della classe c#
- Aggiunto `id_gettext` per contenere il riferimento JNI di Java `getText` (metodo)
- Incluso un `(IntPtr, JniHandleOwnership)` costruttore
- Implementato `ThresholdType` e `ThresholdClass` come requisito per xamarin conoscere i dettagli di `Invoker`
- `GetText` necessario per la ricerca di Java `getText` metodo con la firma JNI appropriata e chiamarlo
- `Dispose` è necessario solo per cancellare il riferimento `class_ref`

Dopo l'aggiunta di questa classe e la generazione di un nuovo AAR, nostro unit test passa. Come si può notare questo modello per i callback non è *ideale*, ma fattibile.

Per informazioni dettagliate sull'interoperabilità di linguaggio, vedere la straordinaria [documentazione di xamarin](~/android/platform/java-integration/working-with-jni.md) su questo argomento.

## <a name="interfaces"></a>Interfacce

Le interfacce sono molto simile a classi astratte, ad eccezione di un dettaglio: xamarin non generare Java per gli utenti. Infatti, prima di incorporare .NET, non sono presenti molti scenari in cui Java implementa un'interfaccia in c#.

Si supponga che abbiamo l'interfaccia in c# seguente:

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` segnala all'incorporamento di .NET che si tratta di un'interfaccia di xamarin. Android, ma in caso contrario equivale esattamente come un `abstract` classe.

Poiché attualmente xamarin non genererà il codice Java per questa interfaccia, aggiungere al progetto c# di Java seguente:

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

È possibile inserire il file in un punto qualsiasi, ma assicurarsi di impostare l'azione di compilazione `AndroidJavaSource`. Ciò segnalerà .NET incorporamento per copiare la directory appropriata in cui vengono compilati in file AAR.

Successivamente, il `Invoker` è piuttosto stessa implementazione:

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

Dopo aver generato un file AAR, test in Android Studio potremmo l'unità di passaggio seguente:

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

Override di un `virtual` in Java è possibile, ma non un'esperienza ottimale.

Si supponga di che disporre la classe c# seguente:

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

Se è stata seguita la `abstract` classe esempio precedente funzionerà ad eccezione di un dettaglio: _xamarin non cercare di `Invoker`_ .

Per risolvere questo problema, modificare la classe c# per essere `abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

Non è ideale, ma ottiene in questo scenario. Xamarin selezionerà il `VirtualClassInvoker` e Java è possibile utilizzare `@Override` sul metodo.

## <a name="callbacks-in-the-future"></a>Callback in futuro

Esistono un paio di elementi possibile per migliorare questi scenari:

1. `throws Throwable` in c# è fisso costruttori su questo [PR](https://github.com/xamarin/java.interop/pull/170).
1. Rendere il generatore di Java in xamarin supporta le interfacce.
    - Questo rimuove la necessità di aggiungere il file di origine Java con un'azione di compilazione di `AndroidJavaSource`.
1. Un modo per xamarin caricare un `Invoker` per classi virtuali.
    - Questo elimina la necessità di contrassegnare la classe nel nostro `virtual` esempio `abstract`.
1. Generare `Invoker` automaticamente le classi per l'incorporamento di .NET
    - Questo deve essere complessa, ma fattibile. Xamarin è già un comportamento simile al seguente per i progetti di associazione di Java.

È presente una grande quantità di lavoro da eseguire qui, ma tali miglioramenti all'incorporamento .NET sono possibili.

## <a name="further-reading"></a>Ulteriori informazioni

* [Guida introduttiva in Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Ricerca di Android preliminare](~/tools/dotnet-embedding/android/index.md)
* [Limitazioni di incorporamento .NET](~/tools/dotnet-embedding/limitations.md)
* [Che contribuiscono al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
