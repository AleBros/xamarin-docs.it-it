---
title: Come funzionano i provider di contenuti
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9d8710b3b88b59871b88a1d42ec4f4bb3e515ff5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756512"
---
# <a name="how-content-providers-work"></a>Come funzionano i provider di contenuti

Esistono due classi che coinvolgono un' `ContentProvider` interazione:

- **ContentProvider** &ndash; Implementa un'API che espone un set di dati in modo standard. I metodi principali sono query, INSERT, Update e DELETE.

- **ContentResolver** Proxy statico che comunica con un oggetto `ContentProvider` per accedere ai dati, dall'interno della stessa applicazione o da un'altra applicazione. &ndash;

Un provider di contenuti è in genere supportato da un database SQLite, ma l'API significa che l'utilizzo di codice non richiede informazioni sull'SQL sottostante. Le query vengono eseguite tramite un URI usando costanti per fare riferimento ai nomi delle colonne (per ridurre le dipendenze dalla struttura di dati `ICursor` sottostante) e viene restituito un oggetto per l'iterazione del codice di consumo.

## <a name="consuming-a-contentprovider"></a>Utilizzo di un ContentProvider

`ContentProviders`esporre la funzionalità tramite un URI registrato in **file AndroidManifest. XML** dell'applicazione che pubblica i dati. Esiste una convenzione in cui l'URI e le colonne di dati esposte devono essere disponibili come costanti per semplificare l'associazione ai dati. `ContentProviders` Le funzionalità predefinite di Android forniscono classi di praticità con costanti che fanno riferimento alla struttura dei dati [`Android.Providers`](xref:Android.Provider) nello spazio dei nomi.

### <a name="built-in-providers"></a>Provider predefiniti

Android consente di accedere a un'ampia gamma di dati di sistema e `ContentProviders`utente tramite:

- *Browser* segnalibri e cronologia del browser (sono `READ_HISTORY_BOOKMARKS` necessarie l'autorizzazione `WRITE_HISTORY_BOOKMARKS`e/o). &ndash;

- *CallLog* &ndash; chiamate recenti effettuate o ricevute con il dispositivo.

- *Contatti* &ndash; informazioni dettagliate dall'elenco dei contatti dell'utente, tra cui persone, telefoni, foto & gruppi.

- *Mediastore* &ndash; contenuto del dispositivo dell'utente: audio (album, artisti, generi, playlist), immagini (incluse le anteprime) & video.

- *Impostazioni* di &ndash; impostazioni e preferenze del dispositivo a livello di sistema.

- *UserDictionary* &ndash; contenuto del dizionario definito dall'utente utilizzato per l'input di testo predittivo.

- *Messaggio vocale* &ndash; cronologia dei messaggi della segreteria telefonica.

## <a name="classes-overview"></a>Cenni preliminari sulle classi

Di seguito sono illustrate le classi primarie utilizzate quando si utilizza un oggetto `ContentProvider` :

[![Diagramma classi dell'applicazione del provider di contenuti e utilizzo delle interazioni con le applicazioni](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

In questo diagramma, `ContentProvider` implementa query e registra gli URI utilizzati da altre applicazioni per individuare i dati. Funge da "proxy" `ContentProvider` per (metodi di query, inserimento, aggiornamento ed eliminazione). `ContentResolver` Il `SQLiteOpenHelper` contiene i dati utilizzati `ContentProvider`da, ma non è esposto direttamente alle applicazioni che utilizzano.
L' `CursorAdapter` oggetto passa il cursore restituito dall' `ContentResolver` oggetto da visualizzare in `ListView`un oggetto. È `UriMatcher` una classe helper che analizza gli URI durante l'elaborazione delle query.

Lo scopo di ogni classe è descritto di seguito:

- **ContentProvider** &ndash; Implementare i metodi della classe astratta per esporre i dati. L'API viene resa disponibile ad altre classi e applicazioni tramite l'attributo URI aggiunto alla definizione della classe.

- **SQLiteOpenHelper** Consente di implementare l'archivio dati SQLite esposto da. `ContentProvider` &ndash;

- **UriMatcher** &ndash; Utilizzare nell'implementazione`ContentProvider` di per gestire gli URI utilizzati per eseguire una query sul contenuto. `UriMatcher`

- **ContentResolver** L'utilizzo del codice utilizza `ContentResolver` un oggetto per `ContentProvider` accedere a un'istanza di. &ndash; Le due classi si occupano insieme dei problemi di comunicazione tra processi, consentendo la condivisione semplice dei dati tra le applicazioni. L'utilizzo del codice non crea `ContentProvider` mai una classe in modo esplicito, bensì l'accesso ai dati tramite la creazione di un cursore basato su `ContentProvider` un URI esposto dall'applicazione.

- **CursorAdapter** &ndash; `ContentProvider`Usare `CursorAdapter` o pervisualizzareidatiacui`SimpleCursorAdapter` si accede tramite un.

L' `ContentProvider` API consente ai consumer di eseguire una serie di operazioni sui dati, ad esempio:

- Eseguire query sui dati per restituire elenchi o singoli record.
- Modificare i singoli record.
- Aggiungere nuovi record.
- Eliminare i record.

Questo documento contiene un esempio che usa un sistema fornito `ContentProvider`, oltre a un semplice esempio di sola lettura che implementa un oggetto personalizzato. `ContentProvider`
