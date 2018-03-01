---
title: Lavoro provider come contenuto
ms.topic: article
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 142ef16606bbf47de073122791fa2509ed6b6353
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="how-content-providers-work"></a>Lavoro provider come contenuto

Esistono due classi coinvolte in un `ContentProvider` interazione:

- **Provider di contenuti** &ndash; implementa un'API che espone un set di dati in modo standard. I metodi principali sono Query, inserimento, aggiornamento ed eliminazione.

- **ContentResolver** &ndash; un proxy statico che comunica con un `ContentProvider` per accedere ai dati, un altro all'interno della stessa applicazione o da un'altra applicazione.

Un provider di contenuti in genere è supportato da un database SQLite, ma l'API significa che utilizza codice non è necessario conoscere il SQL sottostante. Le query vengono eseguite tramite un Uri con costanti per fare riferimento a nomi di colonna (per ridurre le dipendenze sulla struttura di dati sottostante) e un `ICursor` viene restituito per il codice utilizzato scorrere.

<a name="Consuming_a_ContentProvider" />

## <a name="consuming-a-contentprovider"></a>Utilizzo di un provider di contenuti

`ContentProviders` esporre le proprie funzionalità tramite un Uri che viene registrato nel **AndroidManifest.xml** dell'applicazione che pubblica i dati. È una convenzione in cui l'Uri e le colonne di dati che vengono esposti devono essere disponibile come costanti per facilitare ai dati. Android predefiniti del `ContentProviders` tutti forniscono classi di praticità con costanti che fanno riferimento la struttura dei dati nel [ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/) dello spazio dei nomi.


<a name="Built-In_Providers" />

### <a name="built-in-providers"></a>Provider predefiniti

Android consente di accedere a un'ampia gamma di sistema e dati utente tramite `ContentProviders`:

- *Browser* &ndash; segnalibri e della cronologia del browser (è necessaria l'autorizzazione `READ_HISTORY_BOOKMARKS` e/o `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; chiamate recenti apportate o ricevuti con il dispositivo.

- *Contatti* &ndash; informazioni dall'elenco di contatti dell'utente, ad esempio persone, telefoni, foto e gruppi.

- *MediaStore* &ndash; contenuto del dispositivo dell'utente: audio (album, artisti, generi, playlist), (incluse le anteprime) di immagini e video.

- *Impostazioni* &ndash; preferenze e le impostazioni del dispositivo a livello di sistema.

- *UserDictionary* &ndash; contenuto del dizionario definito dall'utente usato per l'input di testo predittiva.

- *Messaggio vocale* &ndash; cronologia dei messaggi di posta vocale.


<a name="Classes_Overview" />

## <a name="classes-overview"></a>Cenni preliminari sulle classi

Le principali classi utilizzate con un `ContentProvider` riportati di seguito:

[![Diagramma di classi di applicazioni Provider di contenuti e le interazioni tra applicazioni consumo](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png)

In questo diagramma il `ContentProvider` implementa le query e registra l'URI che usati per individuare i dati da altre applicazioni. Il `ContentResolver` funge da proxy' ' per il `ContentProvider` (Query, inserimento, aggiornamento ed eliminazione metodi). Il `SQLiteOpenHelper` contiene i dati utilizzati per il `ContentProvider`, ma non è esposta direttamente di che usano le app.
Il `CursorAdapter` passa il cursore restituito dal `ContentResolver` per visualizzare in un `ListView`. Il `UriMatcher` è una classe helper che consente di analizzare gli URI durante l'elaborazione di query.

Lo scopo di ogni classe è descritta di seguito:

- **Provider di contenuti** &ndash; implementare i metodi della classe astratta per esporre i dati. L'API viene resa disponibile per altre classi e le applicazioni tramite l'attributo Uri che viene aggiunto alla definizione della classe.

- **SQLiteOpenHelper** &ndash; consente di implementano l'archivio dati SQLite esposto dal `ContentProvider`.

- **UriMatcher** &ndash; utilizzare `UriMatcher` nel `ContentProvider` implementazione per gestire gli URI che vengono utilizzati per la query del contenuto.

- **ContentResolver** &ndash; utilizza codice utilizza un `ContentResolver` per accedere a un `ContentProvider` istanza. Le due classi insieme svolgere i problemi di comunicazione tra processi, consentendo di essere facilmente condiviso tra le applicazioni dati. Mai utilizza codice crea un `ContentProvider` classe in modo esplicito; in alternativa, in cui si accedono ai dati tramite la creazione di un cursore in base a un Uri esposto dal `ContentProvider` applicazione.

- **CursorAdapter** &ndash; utilizzare `CursorAdapter` o `SimpleCursorAdapter` per visualizzare i dati accessibili tramite un `ContentProvider`.

Il `ContentProvider` API consente agli utenti di eseguire una serie di operazioni sui dati, ad esempio:

-  Eseguire query sui dati per restituire gli elenchi o singoli record.
-  Modificare i singoli record.
-  Aggiunta di nuovi record.
-  Eliminare i record.

Questo documento contiene un esempio che usa una fornita dal sistema `ContentProvider`, nonché un semplice esempio di sola lettura che implementa un oggetto personalizzato `ContentProvider`.

