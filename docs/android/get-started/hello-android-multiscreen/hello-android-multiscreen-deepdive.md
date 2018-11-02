---
title: 'Hello, Android Multiscreen: approfondimenti'
description: In questa guida in due parti, l'applicazione Phoneword, creata nella guida Hello, Android, viene espansa in modo da gestire una seconda schermata. Nello stesso tempo vengono presentati i blocchi predefiniti di base per le applicazioni Android. È incluso anche un approfondimento dell'architettura Android, per consentire una maggiore comprensione della struttura e delle funzionalità delle applicazioni Android.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E4150036-7760-4023-BD33-B7BDE7B7AF5B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: 3eee66032a33e66d3a6a22ca43cb931fbd59888f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122985"
---
# <a name="hello-android-multiscreen-deep-dive"></a>Hello, Android Multiscreen: approfondimenti

_In questa guida in due parti l'applicazione Phoneword, creata nella guida Hello, Android, viene espansa per poter gestire una seconda schermata. Nello stesso tempo vengono presentati i blocchi predefiniti di base per le applicazioni Android. È incluso anche un approfondimento dell'architettura Android, per consentire una maggiore comprensione della struttura e delle funzionalità delle applicazioni Android._

In [Hello, Android Multiscreen: guida introduttiva](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md) è stata compilata ed eseguita la prima applicazione Xamarin.Android multi-schermata.

In questa guida viene illustrata l'architettura Android più avanzata. Verranno anche illustrate le funzioni di spostamento in Android tramite *Intent*, nonché le opzioni di spostamento hardware. Le nuove aggiunte all'app Phoneword verranno descritte in dettaglio man mano che si verrà sviluppando una visione olistica delle relazioni con il sistema operativo e con le altre applicazioni.

## <a name="android-architecture-basics"></a>Introduzione all'architettura Android

In [Hello, Android: approfondimenti](~/android/get-started/hello-android/hello-android-deepdive.md) si è appreso che le applicazioni Android sono programmi univoci, perché non hanno un punto di ingresso singolo. Il sistema operativo (o un'altra applicazione), invece, avvia una delle attività registrate dell'applicazione, che a sua volta avvia il processo per l'applicazione. Questo approfondimento dell'architettura Android amplia la comprensione del modo in cui vengono costruite le applicazioni Android, presentando i blocchi predefiniti per le applicazioni Android e le loro funzioni.

### <a name="android-application-building-blocks"></a>Blocchi di compilazione dell'applicazione Android

Un'applicazione Android è costituita da una raccolta di classi Android speciali denominate *blocchi applicazione*. I blocchi, forniti in bundle con un numero qualsiasi di risorse app (immagini, temi, classi helper e così via) vengono coordinati da un file XML denominato *manifesto Android*.

I blocchi applicazione costituiscono la colonna portante delle applicazioni Android, perché consentono di eseguire operazioni che in genere non è possibile eseguire con le classi normali. I due blocchi più importanti sono _Attività_ e _Servizi_:

- **Attività** &ndash; Un'attività corrisponde a una schermata con un'interfaccia utente ed è concettualmente simile a una pagina Web in un'applicazione Web. In un'applicazione newsfeed, ad esempio, la schermata di accesso rappresenta la prima attività, l'elenco scorrevole delle notizie rappresenta un'altra attività e la pagina dei dettagli per ogni elemento rappresenta una terza attività. Altre informazioni sulle attività sono disponibili nella guida [Ciclo di vita delle attività](~/android/app-fundamentals/activity-lifecycle/index.md).

- **Servizio** &ndash; I servizi Android supportano le attività facendosi carico delle attività a esecuzione prolungata ed eseguendo queste ultime in background. I servizi, che non hanno un'interfaccia utente, vengono usati per gestire attività non associate a schermate &ndash; ad esempio la riproduzione di un brano musicale in background o il caricamento di foto in un server. Per altre informazioni sui servizi, vedere le guide [Creating Services](~/android/app-fundamentals/services/index.md) (Creazione di servizi) e [Android Services](~/android/app-fundamentals/services/index.md) (Servizi Android).

Un'applicazione Android non può usare tutti i tipi di blocchi e spesso contiene diversi blocchi di un unico tipo. Ad esempio, l'applicazione Phoneword in [Hello, Android: guida introduttiva](~/android/get-started/hello-android/hello-android-quickstart.md) è composta da una sola attività (schermata) e da alcuni file di risorse. Un'app lettore musicale semplice può avere diverse attività e un servizio per la riproduzione di musica quando l'app è in background.

### <a name="intents"></a>Intent

Un altro concetto fondamentale nelle applicazioni Android è quello di *Intent*.
Android si basa sul *principio dei privilegi minimi*: le applicazioni hanno accesso solo ai blocchi necessari per il proprio funzionamento e hanno accesso limitato ai blocchi che costituiscono il sistema operativo o altre applicazioni. Analogamente, i blocchi sono *poco accoppiati*: sono progettati in modo da avere scarsa conoscenza di altri blocchi e accesso limitato a questi, anche per quanto riguarda i blocchi che fanno parte della stessa applicazione.

Per comunicare, i blocchi applicazione inviano e ricevono messaggi asincroni detti *Intent*. Gli Intent contengono informazioni sul blocco di destinazione. A volte contengono anche alcuni dati. Un Intent inviato da un componente di un'app attiva un evento in un altro componente dell'app, associando i due componenti e consentendo a questi di comunicare. Inviando e ricevendo Intent, è possibile consentire ai blocchi di coordinare operazioni complesse, ad esempio l'avvio dell'app della fotocamera per eseguire foto e salvarle, la raccolta di informazioni sulla posizione o il passaggio da una schermata alla successiva.

### <a name="androidmanifestxml"></a>AndroidManifest.XML

Quando si aggiunge un blocco all'applicazione, questo viene registrato con un file XML speciale denominato **manifesto Android**. Il manifesto tiene traccia di tutti i blocchi in un'applicazione, nonché dei requisiti di versione, delle autorizzazioni e delle librerie collegate &ndash; in altre parole, di tutto ciò che il sistema operativo deve sapere per consentire l'esecuzione dell'applicazione. Tramite le attività e gli Intent il **manifesto Android** determina anche quali azioni siano appropriate per ogni attività. Queste funzionalità avanzate del manifesto Android sono trattate nella guida [Working with the Android Manifest](~/android/platform/android-manifest.md) (Uso del manifesto Android).

Nella versione a schermata singola dell'applicazione Phoneword sono stati usati una sola attività, un solo Intent e `AndroidManifest.xml`, oltre a risorse aggiuntive come le icone. Nella versione multi-schermata di Phoneword è stata aggiunta un'attività e l'applicazione è stata avviata dalla prima attività tramite un Intent. La sezione successiva prende in esame il modo in cui gli Intent consentono di eseguire spostamenti all'interno delle applicazioni Android.

## <a name="android-navigation"></a>Spostamento in Android

Per passare da una schermata all'altra sono stati usati Intent. È ora il momento di approfondire il codice usato a questo scopo per vedere come funzionano gli Intent e comprendere il ruolo di questi ultimi nelle operazioni di spostamento in Android.

### <a name="launching-a-second-activity-with-an-intent"></a>Avviare una seconda attività con un Intent

Nell'applicazione Phoneword è stato usato un Intent per avviare una seconda schermata (attività). Iniziare creando un Intent, passando l'oggetto *Context* corrente (`this`che fa riferimento all'oggetto **Context**) e il tipo del blocco applicazione che si sta cercando (`TranslationHistoryActivity`):

```csharp
Intent intent = new Intent(this, typeof(TranslationHistoryActivity));
```

**Context** è un'interfaccia alle informazioni globali relative all'ambiente dell'applicazione &ndash; consente agli oggetti appena creati di sapere cosa avviene nell'applicazione stessa. Usando un'analogia con i messaggi, si specifica il nome del destinatario del messaggio (`TranslationHistoryActivity`) e l'indirizzo del ricevitore (`Context`).

Android offre la possibilità di allegare dati semplici a un Intent (i dati complessi vengono gestiti in modo diverso). Nell'esempio relativo a Phoneword, si usa `PutStringArrayExtra` per allegare un elenco di numeri di telefono all'Intent e si richiama `StartActivity` per il destinatario dell'Intent. Ecco il codice completo:

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", _phoneNumbers);
    StartActivity(intent);
};
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Altri concetti introdotti in Phoneword

L'applicazione Phoneword ha introdotto alcuni concetti non trattati in questa guida. Essi includono:

**Risorse stringa** &ndash; Nell'applicazione Phoneword, il testo di `TranslationHistoryButton` è stato impostato su `"@string/translationHistory"`. La sintassi di `@string` indica che il valore della stringa viene archiviato nel _file di risorse stringa_, **Strings.xml**. A **Strings.xml** è stato aggiunto il valore della stringa `translationHistory` seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Call History</string>
</resources>
```

Per altre informazioni sulle risorse stringa e su altre risorse Android, vedere la guida [Risorse Android](~/android/app-fundamentals/resources-in-android/index.md).

**ListView e ArrayAdapter** &ndash; Un _ListView_, un componente dell'interfaccia utente, rappresenta un modo semplice per presentare un elenco di righe a scorrimento. Un'istanza di `ListView` richiede un _Adapter_ da cui ricevere i dati presenti nelle visualizzazioni di righe. La riga di codice seguente è stata usata per popolare l'interfaccia utente di `TranslationHistoryActivity`:

```csharp
this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
```

ListView e Adapter non rientrano nell'ambito di questo documento, ma vengono trattati in modo completo nella guida [ListViews and Adapters](~/android/user-interface/layouts/list-view/index.md) (ListViews e Adapter).
[Popolamento di un controllo ListView con dati](~/android/user-interface/layouts/list-view/populating.md) riguarda in modo specifico l'uso delle classi `ListActivity` e `ArrayAdapter` predefinite per creare e popolare un elemento `ListView` senza definire un layout personalizzato, come è stato fatto nell'esempio Phoneword.

## <a name="summary"></a>Riepilogo

La prima applicazione multi-schermata Android è stata completata. Questa guida ha presentato i *blocchi predefiniti per le applicazioni Android* e gli *Intent* e li ha usati per creare un'applicazione Android multi-schermata. Sono state acquisite le competenze di base necessarie per iniziare a sviluppare applicazioni Xamarin.Android personali.

Nelle guide [Building Cross-Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) (Creazione di applicazioni multipiattaforma) si apprenderà come creare applicazioni multipiattaforma con Xamarin.
