---
title: Contacts e ContactsUI in xamarin. IOS
description: Questo articolo illustra l'utilizzo con i nuovi contatti e i contatti dell'interfaccia utente di Framework in un'app xamarin. IOS. Questi Framework sostituiscono la Rubrica esistente e dell'interfaccia utente della Rubrica indirizzi usati nelle versioni precedenti di iOS.
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: e3f1533605d08df58d8d257714dd8135690c0e5d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105454"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Contacts e ContactsUI in xamarin. IOS

_Questo articolo illustra l'utilizzo con i nuovi contatti e i contatti dell'interfaccia utente di Framework in un'app xamarin. IOS. Questi Framework sostituiscono la Rubrica esistente e dell'interfaccia utente della Rubrica indirizzi usati nelle versioni precedenti di iOS._

Con l'introduzione di iOS 9, Apple ha rilasciato due nuovi Framework, `Contacts` e `ContactsUI`, tale sostituzione l'esistente Rubrica e Framework dell'interfaccia utente della Rubrica indirizzi usati da iOS 8 e versioni precedenti.

I due nuovi Framework contengono le funzionalità seguenti:

- [**Contatti** ](#contacts) -fornisce accesso ai dati di elenco contatti dell'utente.
    Poiché la maggior parte delle App richiedono solo accesso in sola lettura, questo framework è stato ottimizzato per l'accesso thread-safe di sola lettura.

- [**ContactsUI** ](#contactsui) -interfaccia utente di xamarin. IOS offre elementi da visualizzare, modificare, selezionare e creare contatti nei dispositivi iOS.

[![](contacts-images/add01.png "Un esempio di foglio di contatto in un dispositivo iOS")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> L'oggetto esistente `AddressBook` e `AddressBookUI` Framework utilizzato da iOS 8 (e versioni precedenti) sono deprecati in iOS 9 e deve essere sostituiti con il nuovo `Contacts` e `ContactsUI` Framework appena possibile per le app xamarin. IOS esistente. Nuove app devono essere scritti da nuovi Framework.




Nelle sezioni seguenti, articolo verrà fornita un'occhiata a questi nuovi Framework e come implementarli in un'app xamarin. IOS.

<a name="contacts" />

## <a name="the-contacts-framework"></a>Il Framework di contatti

Il Framework di contatti fornisce accesso di xamarin. IOS per le informazioni di contatto dell'utente. Poiché la maggior parte delle App richiedono solo accesso in sola lettura, questo framework è stato ottimizzato per l'accesso thread-safe di sola lettura.

<a name="Contact_Objects" />

### <a name="contact-objects"></a>Oggetti del contatto

Il `CNContact` classe fornisce thread-safe di sola lettura e accesso alle proprietà di un contatto, ad esempio nome, indirizzo o i numeri di telefono. `CNContact` le funzioni come un `NSDictionary` e contiene più raccolte di sola lettura di proprietà (ad esempio, indirizzi o numeri di telefono):

[![](contacts-images/contactobjects.png "Panoramica di oggetti contatto")](contacts-images/contactobjects.png#lightbox)

Per qualsiasi proprietà che può avere più valori (ad esempio i numeri di telefono o indirizzo di posta elettronica), verrà rappresentato come una matrice di `NSLabeledValue` oggetti. `NSLabeledValue` è una tupla di thread-safe costituito da un set di sola lettura di etichette e i valori in cui l'etichetta definisce il valore per l'utente (ad esempio posta elettronica di domestica o aziendale). Il framework di contatti fornisce una selezione di etichette predefinite (tramite il `CNLabelKey` e `CNLabelPhoneNumberKey` le classi statiche) che è possibile usare nell'app o è possibile scegliere di definire le etichette personalizzate per le proprie esigenze.

Per qualsiasi app di xamarin. IOS che è necessario modificare i valori di un contatto esistente (o crearne uno nuovo), usare il `NSMutableContact` versione della classe e le relative classi sub (ad esempio `CNMutablePostalAddress`).

Nel codice seguente verrà, ad esempio, creare un nuovo contatto e aggiungerlo alla raccolta dell'utente dei contatti:

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

Il framework di contatti contiene diversi oggetti e i metodi che consentono di formattano e localizzare il contenuto per la visualizzazione all'utente. Ad esempio, il codice seguente sarebbe formattare correttamente un nome di contatti e dell'indirizzo per la visualizzazione:

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

Per le etichette di proprietà che si prevede di visualizzare nell'interfaccia utente dell'app, il framework di contatto dispone di metodi per la localizzazione anche tali stringhe. Anche in questo caso si basa sulle impostazioni locali correnti del dispositivo iOS in che l'app è in esecuzione. Ad esempio:

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>Il recupero dei contatti esistenti

Usando un'istanza di `CNContactStore` (classe), è possibile recuperare le informazioni di contatto dal database dei contatti dell'utente. Il `CNContactStore` contiene tutti i metodi necessari per il recupero o l'aggiornamento dei contatti e gruppi dal database. Poiché questi metodi sono sincroni, è consigliabile eseguirli in un thread in background per evitare che il blocco dell'interfaccia utente.

Usando predicati (compilata dal `CNContact` classe), è possibile filtrare i risultati restituiti durante il recupero dei contatti dal database. Per recuperare solo i contatti che contengono la stringa `Appleseed`, usare il codice seguente:

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> I predicati generici e composti non sono supportati dal framework di contatti.

Ad esempio, per limitare l'operazione di recupero solo il **GivenName** e **FamilyName** le proprietà del contatto, usare il codice seguente:

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

Infine, per eseguire ricerche nel database e restituire i risultati, usare il codice seguente:

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

Se questo codice è stato eseguito dopo l'esempio creato nel **oggetto Contacts** sezione precedente, il risultato restituito sarebbe il contatto "John Appleseed" appena creato.

### <a name="contact-access-privacy"></a>Accesso contatto Privacy

Poiché gli utenti finali possono concedere o negare l'accesso alle informazioni sul contatto per ogni applicazione in base, la prima volta che si effettua una chiamata al `CNContactStore`, verrà visualizzata una finestra di dialogo che richiede di consentire l'accesso per l'app.

La richiesta di autorizzazione solo verrà visualizzata una sola volta, la prima volta che l'app è in esecuzione e i successivi viene eseguito o le chiamate al `CNContactStore` userà l'autorizzazione selezionata dall'utente in quel momento.

È consigliabile progettare l'app in modo che gestisca correttamente l'utente Nega l'accesso ai relativi database dei contatti.

#### <a name="fetching-partial-contacts"></a>Il recupero dei contatti parziali

Oggetto _contattare parziale_ è un contatto che solo alcune delle proprietà disponibili sono stati recuperati dall'archivio di contatto per. Se si tenta di accedere a una proprietà che non è stata recuperata in precedenza, si verificherà un'eccezione.

È possibile controllare con facilità per vedere se un contatto specificato dispone della proprietà desiderata, utilizzando il `IsKeyAvailable` o `AreKeysAvailable` metodi del `CNContact` istanza. Ad esempio:

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
> Il `GetUnifiedContact` e `GetUnifiedContacts` metodi delle `CNContactStore` classe _solo_ restituiscono un contatto parziale limitata per le proprietà richieste dall'operazione di recupero chiavi fornite.

### <a name="unified-contacts"></a>Contatti unificati

Un utente potrebbe avere diverse fonti di informazioni di contatto per una singola persona nel corrispondente database di contatto (ad esempio, iCloud, Facebook o Google Mail). In iOS e OS X App, le informazioni di contatto verranno automaticamente collegate tra loro e visualizzate all'utente come un'unica _unificata contattare_:

[![](contacts-images/unified01.png "Panoramica di contatti unificato")](contacts-images/unified01.png#lightbox)

Questo contatto unificata è una vista temporanea in memoria delle informazioni di contatto di collegamento che verranno assegnate un proprio identificatore univoco (che deve essere usato per ripetere il recupero del contatto se necessario). Per impostazione predefinita, il framework di contatti restituirà un contatto unificata laddove possibile.

### <a name="creating-and-updating-contacts"></a>Creazione e l'aggiornamento dei contatti

Come abbiamo visto nel [oggetti contattare](#Contact_Objects) sezione precedente, si utilizza un `CNContactStore` e un'istanza di un `CNMutableContact` per creare nuovi contatti che vengono quindi scritti per l'utente contatta database usando un `CNSaveRequest`:

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

Oggetto `CNSaveRequest` inoltre consente di memorizzare nella cache più modifiche di contatto e di gruppo in un'unica operazione e tali modifiche al batch di `CNContactStore`.

Per aggiornare un contatto non modificabile ottenuto da un'operazione di recupero, è innanzitutto necessario richiedere una copia modificabile che è quindi modificare e salvare di nuovo l'archivio dei contatti. Ad esempio:

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

Ogni volta che viene modificato un contatto, il contatto Store invia un `CNContactStoreDidChangeNotification` al centro di notifica predefinito. Se si hanno memorizzato nella cache o vengono attualmente visualizzata in tutti i contatti, è necessario aggiornare tali oggetti da Store il contatto (`CNContactStore`).

### <a name="containers-and-groups"></a>I contenitori e i gruppi

Contatti di un utente può essere presente, in locale nel dispositivo dell'utente o come i contatti sincronizzati per il dispositivo da uno o più account server (ad esempio Facebook o Google). Ogni pool di contatti dispone di propri _contenitore_ e un contatto specificato è disponibile solo in un contenitore.

[![](contacts-images/containers01.png "Panoramica dei contenitori e i gruppi")](contacts-images/containers01.png#lightbox)

Alcuni contenitori consentono per i contatti essere disposti in una o più _gruppi_ oppure _sottogruppi_. Questo comportamento è dipendente dall'archivio di backup per un contenitore specificato. Ad esempio, iCloud dispone di un solo contenitore, ma può avere molti gruppi (ma non i sottogruppi). Microsoft Exchange non supporta gruppi d'altra parte, ma può avere più contenitori (uno per ogni cartella di Exchange).

[![](contacts-images/containers02.png "Determinare sovrapposizioni all'interno di contenitori e i gruppi")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>Il Framework ContactsUI

Per le situazioni in cui l'applicazione deve presentare un'interfaccia utente personalizzata, è possibile utilizzare il framework ContactsUI per presentare gli elementi dell'interfaccia utente per visualizzare, modificare, selezionare e creare contatti nell'app xamarin. IOS.

Usando i controlli predefiniti di Apple non solo riduce la quantità di codice che è necessario creare per supportare i contatti nell'app xamarin. IOS, ma è presentare un'interfaccia coerente per gli utenti dell'app.

### <a name="the-contact-picker-view-controller"></a>Il Controller di visualizzazione della selezione contatti

Il Controller di visualizzazione del selettore contatto (`CNContactPickerViewController`) gestisce la visualizzazione di selezione contatto standard che consente all'utente di selezionare un contatto o una proprietà di contatto dal Database di contatto dell'utente. L'utente può selezionare uno o più contatti (di base relativi all'utilizzo) e il Controller di visualizzazione della selezione contattare non richiede l'autorizzazione prima di visualizzare lo strumento di selezione.

Prima di chiamare il `CNContactPickerViewController` (classe), si definiscono le proprietà che l'utente può selezionare e definire i predicati per controllare la visualizzazione e selezione delle proprietà di contatto.

Usare un'istanza della classe che eredita da `CNContactPickerDelegate` per rispondere all'interazione dell'utente con lo strumento di selezione. Ad esempio:

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

Per consentire all'utente di selezionare un indirizzo di posta elettronica nei contatti nel corrispondente database di, è possibile usare il codice seguente:

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

### <a name="the-contact-view-controller"></a>Il Controller di visualizzazione di contatto

Il Controller di visualizzazione di contatto (`CNContactViewController`) classe fornisce un controller per presentare una visualizzazione standard di contatto per l'utente finale. Il contatto è possibile visualizzare nuovi contatti di nuovo, sconosciuto o non esistente e il tipo deve essere specificato prima che venga visualizzata la visualizzazione chiamando il costruttore statico corretto (`FromNewContact`, `FromUnknownContact`, `FromContact`). Di seguito è riportato un esempio:

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di lavorare con i framework di contatto e l'interfaccia utente di contatto in un'applicazione xamarin. IOS. In primo luogo, trattato i diversi tipi di oggetti che fornisce il framework di contatto e come si usarli per accedere ai contatti esistenti oppure crearne di nuovi. È inoltre esaminato framework dell'interfaccia utente di rivolgersi per selezionare i contatti esistenti e visualizzare le informazioni di contatto.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio QuickContacts](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [What ' s New in iOS 9](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Riferimento a Framework di contatti](https://developer.apple.com/documentation/contacts?language=objc)
- [Riferimento a Framework ContactsUI](https://developer.apple.com/documentation/contactsui?language=objc)
