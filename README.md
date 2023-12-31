Δεμέμβρης 2022

Ακολουθώντας κατά γράμμα τις απαιτήσεις ης εκφώνησης, αυτή η εργασία υλοποιεί επικοινωνία μεταξύ παιδιού και γονέα
για την απόκτηση μιας γραμμής κειμένου που είναι "αποθηκευμένο" στον γονέα. 
Αρχικά ακολουθούνται όλες οι απαραίτητες διαδικασίες ορισμού σεμαφόρων και κοινής μνήμης στο αντίστοιχο section ου προγράμματος. 
Αξίζει να σημειωθεί πως η κοινη μνήμη αποτελείται από
α) Έναν πίνακα από συμβολοσειρές που αποτελούν το segment
β) Έναν πίνακα από counters, ένα για κάθε segment
καθώς και διάφορους άλλους counters για τον αριθμό του segment που βρίσκεται στην μνήνη μια δεδομένη στιγμή
αλλά και το αιτούμενο segment.

************************
Κάποιες ειδικές σταθερές
************************

Για την ολοκλήρωση της εργασίας προσπάθησα να δημιουργήσω μια δυναμική προσέγγιση στην κοινή μνήμη. Ωστόσο αυτό αποδείχθηκε
πολύ ιδιαίτερο και προβληματικό οπότε ώρισα ένα max μεγεθος για τη γραμμή αλλά και για τα segment. Αυτές οι σταθερές βρίσκονται στο 
helpers.h

************
Πρώτα στάδια
************

Διαβάζοντας το αρχείο κειμένου, το οποιο ειναι αφιερωμένο στον μεγάλο καλλιτέχνη Keith Haring, δημιουργώ έναν πινακα με συμβολοσειρές
για κάθε γραμμή του. Δημιουργώ στη συνέχεια τα segment στα οποία αναθέτω τις γραμμές. Να σημειωθεί οτι δημιουργώ ξεχωριστή δομή Segment
(struct Segment) η οποία περιέχει έναν πίνακα από δείκτες σε συμβολοσειρές (char***) και έναν counter να κρατάει τον αριθμό των γραμμών. 

Στη συνέχεια δημιουργώ έναν πίνακα από segments και εκτελώ τη διαδικασία ανάθεσεις γραμμών. Αφού συμβεί αυτό και μερικά ακόμη διαδικαστικά
ξεκινάει η δημιουργια των παιδιών.

*******************
Και εγένετο process
*******************

Χρησιμοποιώ την συνάρτηση exec για να απεικονίσω τον κώδικα των παιδιών και για αυτό στον φακελο θα βρείτε τόσο ένα αρχείο για τον πατέρα (parent.c)
όσο και για το παιδί (child.c). 

Το παιδι κάνει τα απαραίτητα init και αν όλα πάνε καλά (**σταυροκοπιέται) θα ξεκινήσει η διαδικασία της αποστολής των αιτημάτων. 

Δημιουργείται τυχαία ένας αριθμός segemnt και ένας αριθμός γραμμής. Δεν έχω υλοποιησει την περίπτωση να ζητηθεί γραμμή που δεν υπάρχει στο segment. 
Γίνονται τα down και ups των σεμαφόρων που πρέπει ακολουθώντας πιστά κάποιες διαφάνειες του βιβλίου του μαθήματος για readers και writers. Πρώτα κατεβαίνει 
ένας σεμαφόρος οποιουδήποτε segment, μετά από την ικανοποιηση κάποιων συνθηκων που επιβάλουν στο process να στείλει αίτημα για segment, το παιδι μπαίνει
σε ένα μέρος του critical section όπου γίνεται η επικοινωνία του αριθμού του segment με τον πατέρα. Αν εκεί δωθεί το ΟΚ στο παιδι να προχωρήσει γιατι δωθηκε
ζητούμενο segment, τότε το παιδι προχωράει και πάει και διαβάζει τη γραμμή που θέλει, ανεβάζοντας τον αντίστοιχο σεμαφόρο για όλα τα process με το ίδιο
ζητούμενο segment και αυξάνοντας, το κάθε ένα, και τον read_counter του αντίστοιχου segment. 

Αφού όλα τα processes διαβάσουν τότε δίνεται ένα ΟΚ στον πατέρα να ελέγσει τι νέο του ζητήθηκε. 

Ο πατέρας από την άλλη, αφού ξεμπλοκάρει, μετά το διάβασμα όλων των process από ένα ζητούμενο segment, έχει να αποφασίσει αν θα ανεβάσει στην μνήμη το ίδιο 
segment ή άλλο. Αυτό γίνεται μέσω της πιθανότητας 0,7 για να μείνει ίδιο, και 0,3 για να αλλάξει. Αν επιλεχθεί να μην αλλάξει ωστόσο υπάρχει και η περίπτωση
να μην έχει ζητηθεί από άλλο process το ίδιο segment όπου και εκεί πρέπει να κάνει αλλάγή. Αυτό γίνεται μέσω των conditions στο if που υπάρχει στον parent μέσα
στο while loop. Εν τέλει ο πατέρας είτε θα αφήσει το segemnt όπως έχει και θα ξανα μπλοκαριστεί ξεπλοκάροντας πρώτα τα παιδιά, είτε θα μπλοκάρει τα παιδιά για 
να αλλάξει το segment και στη συνέχεια θα πράξει με τον ίδιο τρόπο όπως στην πρώτη περίπτωση.

Κάθε παιδι δημιουργεί το δικό του log file στο οποίο και γράφει τις απαραίτητες πληροφορίες. Ωστόσο δεν μου ήταν ιδιαιτέρως σαφές ποιους χρόνους πρέπει 
να γράφουμε στο .log οπότε εκεί ίσως υπάρξει κάποιο λάθος. Εγώ σίγουρα μετράω τον συνολικό χρόνο του αιτήματος, καθώς και τον χρόνο απάντησης ο οποίος
βγαίνει συχνά 0.

*****
Extra
*****

Στον φάκελο μαζί με το αρχείο parent.c και child.c θα βρείτε και ένα ακόμη πηγαίου κώδικα, το helpers.c στο οποίο και τοποθετώ κάποιες σημαντικές συναρτήσεις.
Ακόμη υπάρχει και το includes.h που έχει όλες τις βιβλιοθήκες που χρειάστηκα καθώς και τις σταθερές.

Για να τρέξετε το πρόγραμμα απλά πατήστε:
make child
make erg1
make run 
