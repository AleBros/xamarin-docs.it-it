---
title: Uso del manifesto Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 1438c012608b367c21ebcc401c058b186b917f53
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027807"
---
# <a name="working-with-the-android-manifest"></a>Uso del manifesto Android

**File AndroidManifest. XML** è un file potente della piattaforma Android che consente di descrivere le funzionalità e i requisiti dell'applicazione per Android. Tuttavia, l'utilizzo di tale servizio non è semplice. Xamarin.Android consente di ridurre al minimo questa difficoltà consentendo di aggiungere attributi personalizzati alle classi, che verranno quindi usate per generare automaticamente il manifesto. Il nostro obiettivo è che il 99% degli utenti non debba mai modificare manualmente **file AndroidManifest. XML**. 

**File AndroidManifest. XML** viene generato come parte del processo di compilazione e il codice XML trovato all'interno di **Properties/file AndroidManifest. XML** viene unito a XML generato da attributi personalizzati. Il **file file AndroidManifest. XML** risultante Unito si trova nella sottodirectory **obj** ; ad esempio, si trova in **obj/debug/Android/file AndroidManifest. XML** per le compilazioni di debug. Il processo di Unione è semplice: USA gli attributi personalizzati all'interno del codice per generare elementi XML e *inserisce* tali elementi in **file AndroidManifest. XML**. 

## <a name="the-basics"></a>Nozioni di base

In fase di compilazione, gli assembly vengono analizzati per le classi non`abstract` che derivano dall' [attività](xref:Android.App.Activity) e in cui viene dichiarato l'attributo [`[Activity]`](xref:Android.App.ActivityAttribute) . USA quindi tali classi e attributi per compilare il manifesto. Si consideri il codice di esempio seguente: 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

In questo modo non viene generato alcun risultato in **file AndroidManifest. XML**. Se si vuole che venga generato un elemento `<activity/>`, è necessario usare il [`[Activity]`](xref:Android.App.Activity) 
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

In questo esempio il frammento XML seguente viene aggiunto a **file AndroidManifest. XML**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

L'attributo `[Activity]` non ha alcun effetto sui tipi di `abstract`; i tipi di `abstract` vengono ignorati.

### <a name="activity-name"></a>Nome attività

A partire da Xamarin.Android 5,1, il nome del tipo di un'attività è basato sull'MD5SUM del nome qualificato dall'assembly del tipo esportato. In questo modo è possibile fornire lo stesso nome completo da due assembly diversi e non ottenere un errore di creazione del pacchetto. (Prima di Xamarin.Android 5,1, il nome di tipo predefinito dell'attività è stato creato dallo spazio dei nomi minuscolo e dal nome della classe). 

Se si desidera eseguire l'override di questa impostazione predefinita e specificare in modo esplicito il nome dell'attività, utilizzare la proprietà [`Name`](xref:Android.App.ActivityAttribute.Name) : 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

```xml
<activity android:name="awesome.demo.activity" />
```

> [!NOTE]
> È consigliabile utilizzare la proprietà `Name` solo per motivi di compatibilità con le versioni precedenti, in quanto tale ridenominazione può rallentare la ricerca dei tipi in fase di esecuzione. Se si dispone di codice legacy che prevede che il nome del tipo predefinito dell'attività sia basato sullo spazio dei nomi in minuscolo e sul nome della classe, vedere la pagina relativa alla [denominazione di Android Callable Wrapper](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) per suggerimenti sulla gestione della compatibilità. 

### <a name="activity-title-bar"></a>Barra del titolo attività

Per impostazione predefinita, Android assegna all'applicazione una barra del titolo quando viene eseguita. Il valore usato per questa operazione è [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label). Nella maggior parte dei casi, questo valore è diverso dal nome della classe. Per specificare l'etichetta dell'app sulla barra del titolo, usare la proprietà [`Label`](xref:Android.App.ActivityAttribute.Label) .
Esempio: 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

### <a name="launchable-from-application-chooser"></a>Avviabile da selezione applicazione

Per impostazione predefinita, l'attività non viene visualizzata nella schermata di avvio dell'applicazione Android. Ciò è dovuto al fatto che nell'applicazione saranno presenti molte attività e che non si desidera un'icona per ciascuna di esse. Per specificare quale deve essere avviabile dall'utilità di avvio dell'applicazione, utilizzare la proprietà [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher) . Esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

### <a name="activity-icon"></a>Icona attività

Per impostazione predefinita, all'attività verrà assegnata l'icona di avvio predefinita fornita dal sistema. Per usare un'icona personalizzata, aggiungere prima il **. png** alle **risorse/disegnatore**, impostare l'azione di compilazione su **AndroidResource**, quindi usare la proprietà [`Icon`](xref:Android.App.ActivityAttribute.Icon) per specificare l'icona da usare. Esempio: 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

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

Quando si aggiungono le autorizzazioni per il manifesto Android, come descritto in [aggiungere autorizzazioni al manifesto Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest), queste autorizzazioni vengono registrate in **Properties/file AndroidManifest. XML**. Se, ad esempio, si imposta l'autorizzazione `INTERNET`, viene aggiunto l'elemento seguente a **Properties/file AndroidManifest. XML**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Le compilazioni di debug impostano automaticamente alcune autorizzazioni per semplificare il debug (ad esempio `INTERNET` e `READ_EXTERNAL_STORAGE`) &ndash; queste impostazioni sono impostate solo nell'oggetto **obj/debug/Android/file AndroidManifest. XML** generato e non vengono visualizzate come abilitate nella **richiesta impostazioni delle autorizzazioni** . 

Ad esempio, se si esamina il file manifesto generato in **obj/debug/Android/file AndroidManifest. XML**, è possibile che vengano visualizzati gli elementi di autorizzazione aggiunti seguenti: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Nella versione build di rilascio del manifesto (in **obj/debug/Android/file AndroidManifest. XML**) queste autorizzazioni *non* vengono configurate automaticamente. Se si ritiene che il cambio a una build di rilascio provochi la perdita di un'autorizzazione disponibile nella build di debug da parte dell'app, verificare di aver impostato in modo esplicito questa autorizzazione nelle impostazioni delle **autorizzazioni necessarie** per l'app (vedere **compilare > Android Applicazione** in Visual Studio per Mac; vedere **proprietà > manifesto Android** in Visual Studio). 

## <a name="advanced-features"></a>Funzionalità avanzate

### <a name="intent-actions-and-features"></a>Azioni e funzionalità per finalità

Il manifesto Android fornisce un modo per descrivere le funzionalità dell'attività. Questa operazione viene eseguita tramite gli [Intent](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) e i [`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
attributo personalizzato. È possibile specificare quali azioni sono appropriate per l'attività con la [`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
e quali categorie sono appropriate con la [`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
. È necessario fornire almeno un'attività (motivo per cui le attività vengono fornite nel costruttore). `[IntentFilter]` possono essere fornite più volte e ogni utilizzo produce un elemento `<intent-filter/>` separato all'interno del `<activity/>`. Esempio:

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Questo esempio produce il frammento XML seguente:

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

Il manifesto Android fornisce anche un modo per dichiarare le proprietà per l'intera applicazione. Questa operazione viene eseguita tramite l'elemento `<application>` e la relativa controparte, l'attributo personalizzato dell' [applicazione](xref:Android.App.ApplicationAttribute) . Si noti che si tratta di impostazioni a livello di applicazione (a livello di assembly) anziché di impostazioni per ogni attività. In genere, si dichiara `<application>` proprietà per l'intera applicazione e quindi si esegue l'override di queste impostazioni (in base alle esigenze) per ogni attività. 

Ad esempio, l'attributo `Application` seguente viene aggiunto a **AssemblyInfo.cs** per indicare che l'applicazione può essere sottoposta a debug, che il nome leggibile dall'utente è **My App**e che usa lo stile `Theme.Light` come tema predefinito per tutte le attività: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Questa dichiarazione causa la generazione del frammento XML seguente in **obj/debug/Android/file AndroidManifest. XML**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

In questo esempio, tutte le attività nell'app utilizzeranno per impostazione predefinita lo stile del `Theme.Light`. Se si imposta il tema di un'attività su `Theme.Dialog`, solo tale attività userà lo stile `Theme.Dialog` mentre tutte le altre attività nell'app utilizzeranno per impostazione predefinita lo stile di `Theme.Light` impostato nell'elemento `<application>`. 

L'elemento `Application` non è l'unico modo per configurare `<application>` attributi. In alternativa, è possibile inserire gli attributi direttamente nell'elemento `<application>` di **Properties/file AndroidManifest. XML**. Queste impostazioni vengono unite nell'elemento `<application>` finale che risiede in **obj/debug/Android/file AndroidManifest. XML**. Si noti che il contenuto di **Properties/file AndroidManifest. XML** esegue sempre l'override dei dati forniti dagli attributi personalizzati. 

Sono disponibili molti attributi a livello di applicazione che è possibile configurare nell'elemento `<application>`; Per ulteriori informazioni su queste impostazioni, vedere la sezione [proprietà pubbliche](xref:Android.App.ApplicationAttribute) di [ApplicationAttribute](xref:Android.App.ApplicationAttribute). 

## <a name="list-of-custom-attributes"></a>Elenco di attributi personalizzati

- [Android. app. ActivityAttribute](xref:Android.App.ActivityAttribute) : genera un frammento XML [/manifest/Application/Activity](https://developer.android.com/guide/topics/manifest/activity-element.html) 
- [Android. app. ApplicationAttribute](xref:Android.App.ApplicationAttribute) : genera un frammento XML [/manifest/Application](https://developer.android.com/guide/topics/manifest/application-element.html) 
- [Android. app. InstrumentationAttribute](xref:Android.App.InstrumentationAttribute) : genera un frammento XML [/manifest/Instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) 
- [Android. app. IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) : genera un frammento XML [//Intent-Filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) 
- [Android. app. MetaDataAttribute](xref:Android.App.MetaDataAttribute) : genera un frammento XML [//meta-data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) 
- [Android. app. PermissionAttribute](xref:Android.App.PermissionAttribute) : genera un frammento XML [//Permission](https://developer.android.com/guide/topics/manifest/permission-element.html) 
- [Android. app. PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute) : genera un frammento XML [//Permission-Group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) 
- [Android. app. PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute) : genera un frammento XML [//Permission-Tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) 
- [Android. app. ServiceAttribute](xref:Android.App.ServiceAttribute) : genera un frammento XML [/manifest/Application/Service](https://developer.android.com/guide/topics/manifest/service-element.html) 
- [Android. app. UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute) : genera un frammento XML [/manifest/application/uses-Library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) 
- [Android. app. UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute) : genera un frammento XML [/manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) 
- [Android. Content. BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute) : genera un frammento XML [/manifest/Application/Receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) 
- [Android. Content. ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute) : genera un frammento XML [/manifest/Application/Provider](https://developer.android.com/guide/topics/manifest/provider-element.html) 
- [Android. Content. GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute) : genera un frammento XML [/manifest/Application/Provider/Grant-URI-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html)
