---
title: Contatti e ContactsUI
description: Questo articolo descrive l'utilizzo con i contatti e i contatti UI nuovo framework in un'app xamarin. IOS. Questi Framework sostituiscono la Rubrica esistente e dell'interfaccia utente della Rubrica indirizzi usati nelle versioni precedenti di iOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 996723db83a1f972cce26090d1253f97b6c818d3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="contacts-and-contactsui"></a>Contatti e ContactsUI

_Questo articolo descrive l'utilizzo con i contatti e i contatti UI nuovo framework in un'app xamarin. IOS. Questi Framework sostituiscono la Rubrica esistente e dell'interfaccia utente della Rubrica indirizzi usati nelle versioni precedenti di iOS._

Con l'introduzione di iOS 9, Apple ha rilasciato due nuovi Framework, `Contacts` e `ContactsUI`, tale sostituire l'indirizzo esistente Book e Framework dell'interfaccia utente della Rubrica indirizzi usati da iOS 8 e versioni precedenti.

I due nuovi Framework contengono le funzionalità seguenti:

- [**Contatti** ](#contacts) -fornisce accesso ai dati di contatto dell'utente.
    Poiché la maggior parte delle applicazioni richiedono solo accesso in sola lettura, questo framework è stato ottimizzato per l'accesso thread-safe di sola lettura.

- [**ContactsUI** ](#contactsui) -fornisce xamarin elementi dell'interfaccia utente da visualizzare, modificare, selezionare e creare contatti nei dispositivi iOS.

[![](contacts-images/add01.png "Un esempio di foglio di contatto in un dispositivo iOS")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> **Nota:** esistente `AddressBook` e `AddressBookUI` Framework utilizzato da iOS 8 (e versioni precedenti) sono deprecati in iOS 9 e deve essere sostituiti con il nuovo `Contacts` e `ContactsUI` Framework appena possibile per qualsiasi xamarin esistente app. Nuove app deve essere scritto con il nuovo Framework.




Nelle sezioni seguenti, si verrà esaminato un nuovo Framework e come implementarli in un'app xamarin. IOS.

<a name="contacts" />

## <a name="the-contacts-framework"></a>Il Framework di contatti

Il Framework di contatti xamarin accedere alle informazioni di contatto dell'utente. Poiché la maggior parte delle applicazioni richiedono solo accesso in sola lettura, questo framework è stato ottimizzato per l'accesso thread-safe di sola lettura.

<a name="Contact_Objects" />

### <a name="contact-objects"></a>Oggetti contatto

La `CNContact` classe accesso thread-safe di sola lettura alle proprietà di un contatto, ad esempio nome, indirizzo o numeri di telefono. `CNContact` funziona come un `NSDictionary` e contiene più raccolte di sola lettura di proprietà (ad esempio indirizzi o numeri di telefono):

[![](contacts-images/contactobjects.png "Panoramica di oggetti contatto")](contacts-images/contactobjects.png#lightbox)

Per qualsiasi proprietà che possono avere più valori (ad esempio i numeri di telefono o di indirizzo di posta elettronica), sarà rappresentati come una matrice di `NSLabeledValue` oggetti. `NSLabeledValue` è thread-safe tupla costituita da un set di sola lettura di etichette e i valori in cui l'etichetta definisce il valore per l'utente (ad esempio posta elettronica di domestica o aziendale). Il framework di contatti fornisce una selezione delle etichette predefinite (tramite il `CNLabelKey` e `CNLabelPhoneNumberKey` le classi statiche) che è possibile usare nell'app o è possibile definire le etichette personalizzate per le proprie esigenze.

Per tutte le app xamarin che è necessario modificare i valori di un contatto esistente (o crearne di nuovi), utilizzare il `NSMutableContact` versione della classe e le relative classi sub (ad esempio `CNMutablePostalAddress`).

Nel codice seguente verrà ad esempio, creare un nuovo contatto e aggiungerlo alla raccolta dell'utente di contatti:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

Se questo codice viene eseguito in un dispositivo iOS 9, un nuovo contatto verrà aggiunto alla raccolta dell'utente. Ad esempio:

[![](contacts-images/add01.png "Un nuovo contatto aggiunto alla raccolta dell'utente")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>Formattazione di contatto e la localizzazione

Il framework di contatti contiene diversi oggetti e i metodi che consentono di formattano e localizzare il contenuto da visualizzare all'utente. Ad esempio, il codice seguente sarebbe formattare correttamente un nome di contatti e dell'indirizzo per la visualizzazione:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Per le etichette di proprietà che verranno visualizzati nell'interfaccia utente dell'applicazione, il framework di contatto dispone di metodi per la localizzazione anche tali stringhe. Nuovamente, si basa sulle impostazioni locali correnti del dispositivo iOS che viene eseguita l'app. Ad esempio:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOption.Nickname));
Console.WriteLine(CNLabeledValue.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Recupero di contatti esistenti

Utilizzando un'istanza di `CNContactStore` (classe), è possibile recuperare informazioni dal database dei contatti dell'utente. Il `CNContactStore` contiene tutti i metodi necessari per il recupero o l'aggiornamento di contatti e gruppi dal database. Poiché questi metodi sono sincroni, è consigliabile eseguirli in un thread in background per evitare di bloccare l'interfaccia utente.

Utilizzando i predicati (compilata dalla `CNContact` classe), è possibile filtrare i risultati restituiti durante il recupero di contatti dal database. Per recuperare solo i contatti che contengono la stringa `Appleseed`, utilizzare il codice seguente:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> **Nota:** generico e predicati composti non sono supportati dal framework di contatti.

Ad esempio, per limitare l'operazione di recupero solo il **GivenName** e **FamilyName** le proprietà del contatto, il codice seguente:

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

Infine, per eseguire ricerche nel database e restituire i risultati, utilizzare il codice seguente:

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

Se questo codice è stato eseguito dopo il codice di esempio creati nel **oggetto contatti** sezione precedente, il risultato restituito sarebbe il contatto "John Appleseed" che abbiamo appena creato.

### <a name="contact-access-privacy"></a>Privacy di accesso del contatto

Poiché gli utenti finali possono concedere o negare l'accesso alle informazioni di contatto in ogni applicazione, la prima volta che si effettua una chiamata per il `CNContactStore`, verrà visualizzata una finestra di dialogo in cui viene chiesto di consentire l'accesso per l'app.

La richiesta di autorizzazione solo verrà visualizzata una sola volta, viene eseguita la prima volta che l'applicazione è in esecuzione e quelli successivi o delle chiamate al `CNContactStore` utilizzerà l'autorizzazione selezionato dall'utente in quel momento.

È consigliabile progettare l'app in modo che gestisca correttamente all'utente di negare l'accesso per i database dei contatti.

#### <a name="fetching-partial-contacts"></a>Recupero dei contatti parziali

Oggetto _contattare parziale_ è solo alcune delle proprietà disponibili sono stati recuperati dall'archivio di contatto per un contatto. Se si tenta di accedere a una proprietà che non è stata recuperata in precedenza, si verificherà un'eccezione.

È possibile controllare facilmente per vedere se un determinato contatto dispone della proprietà desiderata, utilizzando il `IsKeyAvailable` o `AreKeysAvailable` metodi del `CNContact` istanza. Ad esempio:

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> **Nota:** il `GetUnifiedContact` e `GetUnifiedContacts` metodi di `CNContactStore` classe _solo_ restituiscono un contatto parziale limitato per le proprietà richieste dall'operazione di recupero chiavi fornite.

### <a name="unified-contacts"></a>Contatti unificati

Un utente può avere diverse fonti di informazioni di contatto per un singolo utente nel corrispondente database contatto (ad esempio iCloud, Facebook o Google posta). In iOS e OS X App, le informazioni di contatto verranno automaticamente collegate tra loro e visualizzate all'utente come una singola _unificata contattare_:

[![](contacts-images/unified01.png "Panoramica di contatti unificata")](contacts-images/unified01.png#lightbox)

Contattare questo unificata è una vista temporanea in memoria il collegamento informazioni di contatto che verranno assegnate un identificatore univoco (che deve essere utilizzato per il contatto recupera di nuovo se necessario). Per impostazione predefinita, il framework di contatti restituirà un contatto unificata laddove possibile.

### <a name="creating-and-updating-contacts"></a>Creazione e l'aggiornamento di contatti

Come illustrato nel [oggetti contatto](#Contact_Objects) sezione precedente, si utilizza un `CNContactStore` e un'istanza di un `CNMutableContact` per creare nuovi contatti che vengono quindi scritti per l'utente. contattare il database utilizzando un `CNSaveRequest`:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

Oggetto `CNSaveRequest` consente anche di memorizzare nella cache più modifiche di contatto e di gruppo in un'unica operazione e le modifiche apportate a batch di `CNContactStore`.

Per aggiornare un contatto non modificabile ottenuto da un'operazione di recupero, è necessario richiedere innanzitutto una copia modificabile che è quindi modificare e salvare di nuovo l'archivio di contatto. Ad esempio:

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>Notifiche di modifica contatto

Ogni volta che viene modificato un contatto, l'archivio di contatto invia un `CNContactStoreDidChangeNotification` l'area di notifica predefinito. Se si hanno memorizzato nella cache o visualizzano di tutti i contatti, è necessario aggiornare gli oggetti dall'archivio di contatto (`CNContactStore`).

### <a name="containers-and-groups"></a>Contenitori e i gruppi

Contatti di un utente può essere presente o in locale nel dispositivo dell'utente come contatti sincronizzati con il dispositivo da uno o più account di server (ad esempio Facebook o Google). Ogni pool di contatti ha il proprio _contenitore_ e un contatto specificato è disponibile solo in un contenitore.

[![](contacts-images/containers01.png "Panoramica di contenitori e gruppi")](contacts-images/containers01.png#lightbox)

Consentano alcuni contenitori per i contatti devono essere disposti in una o più _gruppi_ o _sottogruppi_. Questo comportamento dipende dall'archivio di backup per un contenitore specificato. Ad esempio, iCloud ha un solo contenitore, ma può avere molti gruppi (ma non gruppi). Microsoft Exchange, invece, non supporta gruppi, ma possono essere presenti più contenitori (uno per ogni cartella di Exchange).

[![](contacts-images/containers02.png "Sovrapposta all'interno di contenitori e i gruppi")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>Il Framework ContactsUI

Per le situazioni in cui l'applicazione non devono presentare un'interfaccia utente personalizzata, è possibile utilizzare il framework ContactsUI per presentare gli elementi dell'interfaccia utente per visualizzare, modificare, selezionare e creare i contatti nell'app xamarin. IOS.

Uso di controlli predefiniti di Apple è non solo ridurre la quantità di codice che è necessario creare per il supporto contatti nell'app xamarin, ma un'interfaccia coerente per gli utenti dell'app.

### <a name="the-contact-picker-view-controller"></a>Il Controller di visualizzazione della selezione contatti

Il Controller di visualizzazione selezione contatto (`CNContactPickerViewController`) gestisce la visualizzazione della selezione contatto standard che consente all'utente di selezionare un contatto o una proprietà di contatto da Database di contatto dell'utente. L'utente può selezionare uno o più contatti (basato sull'utilizzo del) e contattare il selettore View Controller non richiede l'autorizzazione prima di visualizzare la selezione.

Prima di chiamare il `CNContactPickerViewController` (classe), si definiscono le proprietà che l'utente può selezionare e definire predicati per controllare la visualizzazione e la selezione delle proprietà di contatto.

Utilizzare un'istanza della classe che eredita da `CNContactPickerDelegate` per rispondere all'interazione dell'utente con il pulsante di selezione. Ad esempio:

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

Per consentire all'utente di selezionare un indirizzo di posta elettronica di contatti nel proprio database, è possibile utilizzare il codice seguente:

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>Il Controller di visualizzazione contatto

Il Controller di visualizzazione di contatto (`CNContactViewController`) fornisce un controller per presentare una visualizzazione standard di contatto per l'utente finale. Il contatto è possibile visualizzare nuovi contatti di nuovo, sconosciuto o esistenti e il tipo deve essere specificato prima che venga visualizzata la visualizzazione chiamando il costruttore statico corretto (`FromNewContact`, `FromUnknownContact`, `FromContact`). Di seguito è riportato un esempio:

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato a un'analisi approfondita funziona con il framework di contatto e l'interfaccia utente di contatto in un'applicazione di xamarin. IOS. In primo luogo, trattati i diversi tipi di oggetti che fornisce il framework di contatto e come si utilizzarli per accedere ai contatti esistenti oppure crearne di nuovi. È inoltre esaminato il framework di interfaccia utente di contattare per selezionare i contatti esistenti e visualizzare le informazioni di contatto.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio QuickContacts](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [Novità di iOS 9](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html#//apple_ref/doc/uid/TP40016198-DontLinkElementID_14))
- [Riferimento di Framework di contatti](https://developer.apple.com/library/prerelease/ios/documentation/Contacts/Reference/Contacts_Framework/index.html#//apple_ref/doc/uid/TP40015328)
- [Riferimento di Framework ContactsUI](https://developer.apple.com/library/prerelease/ios/documentation/ContactsUI/Reference/ContactsUI_Framework/index.html#//apple_ref/doc/uid/TP40016207)
