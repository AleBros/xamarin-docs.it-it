---
title: Utilizzo del manifesto AndroidWorking with the Android Manifest
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 1438c012608b367c21ebcc401c058b186b917f53
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027807"
---
# <a name="working-with-the-android-manifest"></a>Utilizzo del manifesto AndroidWorking with the Android Manifest

**AndroidManifest.xml** è un file potente nella piattaforma Android che consente di descrivere le funzionalità e i requisiti dell'applicazione per Android. Tuttavia, lavorare con esso non è facile. Xamarin.Android consente di ridurre al minimo questa difficoltà consentendo di aggiungere attributi personalizzati alle classi, che verranno quindi utilizzati per generare automaticamente il manifesto per l'utente. Il nostro obiettivo è che il 99% dei nostri utenti non debba mai modificare manualmente **AndroidManifest.xml**. 

**AndroidManifest.xml** viene generato come parte del processo di compilazione e il codice XML trovato all'interno **di Properties/AndroidManifest.xml** viene unito al codice XML generato dagli attributi personalizzati. Il **file AndroidManifest.xml** risultante si trova nella sottodirectory **obj;** ad esempio, si trova in **obj/Debug/android/AndroidManifest.xml** per le compilazioni di Debug.For example, it resides at obj/Debug/android/AndroidManifest.xml for Debug builds. Il processo di unione è semplice: utilizza attributi personalizzati all'interno del codice per generare elementi XML e *inserisce* tali elementi in **AndroidManifest.xml**. 

## <a name="the-basics"></a>Nozioni di base

In fase di compilazione,`abstract` gli assembly vengono analizzati [`[Activity]`](xref:Android.App.ActivityAttribute) per le classi non che derivano da [Activity](xref:Android.App.Activity) e hanno l'attributo dichiarato su di essi. Vengono quindi utilizzati queste classi e attributi per compilare il manifesto. Si consideri il codice di esempio seguente: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

In questo modo non viene generato nulla in **AndroidManifest.xml**. Se si `<activity/>` desidera che venga generato un elemento, è necessario[`[Activity]`](xref:Android.App.Activity) 
attributo personalizzato: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

In questo esempio il seguente frammento xml viene aggiunto a **AndroidManifest.xml:**

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

L'attributo `[Activity]` non `abstract` ha alcun effetto sui tipi; `abstract` vengono ignorati.

### <a name="activity-name"></a>Nome attività

A partire da Xamarin.Android 5.1, il nome del tipo di un'attività si basa sul MD5SUM del nome qualificato dall'assembly del tipo esportato. In questo modo lo stesso nome completo può essere fornito da due assembly diversi e non viene visualizzato un errore di creazione di pacchetti. (Prima di Xamarin.Android 5.1, il nome del tipo predefinito dell'attività è stato creato dallo spazio dei nomi minuscolo e il nome della classe.) 

Se si desidera eseguire l'override di questa impostazione [`Name`](xref:Android.App.ActivityAttribute.Name) predefinita e specificare in modo esplicito il nome dell'attività, utilizzare la proprietà: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

In questo esempio viene prodotto il frammento xml seguente:This example produces the following xml fragment:

```xml
<activity android:name="awesome.demo.activity" />
```

> [!NOTE]
> È consigliabile `Name` utilizzare la proprietà solo per motivi di compatibilità con le versioni precedenti, in quanto tale ridenominazione può rallentare la ricerca dei tipi in fase di esecuzione. Se si dispone di codice legacy che prevede che il nome del tipo predefinito dell'attività sia basato sullo spazio dei nomi minuscolo e il nome della classe, vedere [Android Callable Wrapper Naming](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) per suggerimenti sulla compatibilità. 

### <a name="activity-title-bar"></a>Barra del titolo dell'attività

Per impostazione predefinita, Android fornisce all'applicazione una barra del titolo quando viene eseguita. Il valore utilizzato [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label)per questo è . Nella maggior parte dei casi, questo valore sarà diverso dal nome della classe. Per specificare l'etichetta dell'app sulla [`Label`](xref:Android.App.ActivityAttribute.Label) barra del titolo, usa la proprietà.
Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

In questo esempio viene prodotto il frammento xml seguente:This example produces the following xml fragment:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

### <a name="launchable-from-application-chooser"></a>Avviabile da Application Chooser

Per impostazione predefinita, l'attività non verrà visualizzata nella schermata di avvio delle applicazioni di Android.By default, your activity will not show up in Android's application launcher screen. Questo perché probabilmente ci saranno molte attività nell'applicazione e non si desidera un'icona per ognuno di essi. Per specificare quale deve essere avviabile dall'icona di avvio dell'applicazione, utilizzare la [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher) proprietà . Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

In questo esempio viene prodotto il frammento xml seguente:This example produces the following xml fragment:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

### <a name="activity-icon"></a>Icone Attività

Per impostazione predefinita, all'attività verrà assegnata l'icona di avvio predefinita fornita dal sistema. Per utilizzare un'icona personalizzata, aggiungere innanzitutto il **file PNG** a **Resources/drawable**, impostarne l'azione di compilazione su **AndroidResource**, quindi utilizzare la [`Icon`](xref:Android.App.ActivityAttribute.Icon) proprietà per specificare l'icona da utilizzare. Ad esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

In questo esempio viene prodotto il frammento xml seguente:This example produces the following xml fragment:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

### <a name="permissions"></a>Autorizzazioni

Quando si aggiungono autorizzazioni al manifesto Android (come descritto in [Aggiungi autorizzazioni al manifesto Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), queste autorizzazioni vengono registrate in **Properties/AndroidManifest.xml**. Ad esempio, se `INTERNET` si imposta l'autorizzazione, il seguente elemento viene aggiunto a **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Le build di debug impostano automaticamente alcune `INTERNET` `READ_EXTERNAL_STORAGE`autorizzazioni &ndash; per semplificare il debug (ad esempio e ) queste impostazioni vengono impostate solo nell'obj/Debug/android/AndroidManifest.xml generato e non vengono visualizzate come abilitato nelle impostazioni **delle autorizzazioni necessarie.** **obj/Debug/android/AndroidManifest.xml** 

Ad esempio, se si esamina il file manifesto generato in **obj/Debug/android/AndroidManifest.xml**, è possibile visualizzare i seguenti elementi di autorizzazione aggiunti: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Nella versione della build di rilascio del manifesto (in **obj/Debug/android/AndroidManifest.xml**), queste autorizzazioni *non* vengono configurate automaticamente. Se si scopre che il passaggio a una build di rilascio causa la perdita di un'autorizzazione disponibile nella build di debug, verificare di aver impostato in modo esplicito questa autorizzazione nelle impostazioni **delle autorizzazioni necessarie** per l'app (vedere Build > Applicazione **Android** in Visual Studio per Mac; vedere Proprietà > **Manifesto Android** in Visual Studio). 

## <a name="advanced-features"></a>Funzionalità avanzate

### <a name="intent-actions-and-features"></a>Azioni e funzionalità intento

Il manifesto Android fornisce un modo per descrivere le funzionalità dell'attività. Ciò avviene tramite [Intents](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) e[`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
attributo personalizzato. È possibile specificare quali azioni sono appropriate per la propria attività[`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
costruttore, e quali categorie sono appropriate con il[`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
. È necessario fornire almeno un'attività (per questo motivo le attività vengono fornite nel costruttore). `[IntentFilter]`può essere fornito più volte, e `<intent-filter/>` ogni utilizzo `<activity/>`si traduce in un elemento separato all'interno del file . Ad esempio:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

In questo esempio viene prodotto il frammento xml seguente:This example produces the following xml fragment:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```

### <a name="application-element"></a>Elemento Application

Il manifesto Android fornisce anche un modo per dichiarare le proprietà per l'intera applicazione. Questa operazione viene `<application>` eseguita tramite l'elemento e la relativa controparte, l'attributo personalizzato [Application.This](xref:Android.App.ApplicationAttribute) is done via the element and its counterpart, the Application custom attribute. Si noti che si tratta di impostazioni a livello di applicazione (a livello di assembly) anziché per ogni attività. In genere, `<application>` si dichiarano le proprietà per l'intera applicazione e quindi si esegue l'override di queste impostazioni (se necessario) in base all'attività. 

Ad esempio, `Application` il seguente attributo viene aggiunto a **AssemblyInfo.cs** per indicare che è possibile eseguire il debug `Theme.Light` dell'applicazione, che il nome leggibile dall'utente è **App**personale e che utilizza lo stile come tema predefinito per tutte le attività: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Questa dichiarazione fa sì che il seguente frammento XML venga generato in **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

In questo esempio, per tutte le `Theme.Light` attività dell'app verrà impostato lo stile per impostazione predefinita. Se imposti il tema `Theme.Dialog`di un'attività su `Theme.Dialog` , solo quell'attività utilizzerà `Theme.Light` lo stile mentre `<application>` tutte le altre attività nell'app utilizzeranno per impostazione predefinita lo stile impostato nell'elemento. 

L'elemento `Application` non è l'unico modo per configurare `<application>` gli attributi. In alternativa, è possibile inserire `<application>` gli attributi direttamente nell'elemento di **Properties/AndroidManifest.xml**. Queste impostazioni vengono unite `<application>` nell'elemento finale che risiede in **obj/Debug/android/AndroidManifest.xml**. Si noti che il contenuto di **Properties/AndroidManifest.xml** esegue sempre l'override dei dati forniti dagli attributi personalizzati. 

Esistono molti attributi a livello di applicazione `<application>` che è possibile configurare nell'elemento; Per ulteriori informazioni su queste impostazioni, vedere la sezione [Public Properties](xref:Android.App.ApplicationAttribute) di [ApplicationAttribute](xref:Android.App.ApplicationAttribute). 

## <a name="list-of-custom-attributes"></a>Elenco di attributi personalizzati

- [Android.App.ActivityAttribute:](xref:Android.App.ActivityAttribute) genera un frammento XML [/manifest/application/activity](https://developer.android.com/guide/topics/manifest/activity-element.html) 
- [Android.App.ApplicationAttribute:](xref:Android.App.ApplicationAttribute) genera un frammento XML [/manifest/application](https://developer.android.com/guide/topics/manifest/application-element.html) 
- [Android.App.InstrumentationAttribute:](xref:Android.App.InstrumentationAttribute) genera un frammento XML /manifest/instrumentationAndroid.App.InstrumentationAttribute : Generates a [/manifest/instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) XML fragment 
- [Android.App.IntentFilterAttribute:](xref:Android.App.IntentFilterAttribute) genera un frammento XML [//intent-filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) 
- [Android.App.MetaDataAttribute:](xref:Android.App.MetaDataAttribute) genera un frammento XML [//meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) 
- [Android.App.PermissionAttribute:](xref:Android.App.PermissionAttribute) genera un frammento XML [//permission](https://developer.android.com/guide/topics/manifest/permission-element.html) 
- [Android.App.PermissionGroupAttribute:](xref:Android.App.PermissionGroupAttribute) genera un frammento XML [//permission-group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) 
- [Android.App.PermissionTreeAttribute:](xref:Android.App.PermissionTreeAttribute) genera un frammento XML [//permission-tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) 
- [Android.App.ServiceAttribute:](xref:Android.App.ServiceAttribute) genera un frammento XML [/manifest/application/service](https://developer.android.com/guide/topics/manifest/service-element.html) 
- [Android.App.UsesLibraryAttribute:](xref:Android.App.UsesLibraryAttribute) genera un frammento XML [/manifest/application/uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) 
- [Android.App.UsesPermissionAttribute:](xref:Android.App.UsesPermissionAttribute) genera un frammento XML [/manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) 
- [Android.Content.BroadcastReceiverAttribute:](xref:Android.Content.BroadcastReceiverAttribute) genera un frammento XML [/manifest/application/receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) 
- [Android.Content.ContentProviderAttribute:](xref:Android.Content.ContentProviderAttribute) genera un frammento XML [/manifest/application/provider](https://developer.android.com/guide/topics/manifest/provider-element.html) 
- [Android.Content.GrantUriPermissionAttribute:](xref:Android.Content.GrantUriPermissionAttribute) genera un frammento XML [/manifest/application/provider/grant-uri-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html)
