gem. 07.01.2015 BS1 Prof. Fritzsche

Übungen: http://www2.htw-dresden.de/~fritzsch/BS/bs_uebungen.html

6.1. Aufg. 8.1, 8.2

Beleg: Aufg. 10.5, 10.6, 12.2-12.6 vorzeigen/demonstrieren
13.1.
20.1.
27.1.

8.1
Schreiben Sie eine Shell-Prozedur, die mehrere Dateien aus dem Verzeichnis /glb/studi in ein Unterverzeichnis Ihres Home-Directories kopiert.
Die Namen der zu kopierenden Dateien sind als Parameter zu übergeben, Ihr Unterverzeichnis soll im Dialog abgefragt werden. Sollten kein bzw. mehr als 9 Parameter an die Prozedur übergeben worden sein, ist eine Fehlermeldung auszugeben und die Prozedur zu beenden. Im Fall, daß eine zu kopierende Datei nicht in /glb/studi enthalten ist, ist ebenfalls eine Fehlermeldung auszugeben. 

    #!/bin/sh
    sdir=/glb/studi
    if [ $# -eq 0 -o $# -gt 9 ]; then
    	echo "Fehler: 0 oder mehr als 9 Parameter";
    	echo denkbar sind:
    	ls ${sdir}
    	exit 1;
    fi
    #echo $#: $@
    echo Kopieren der Dateien $@
    echo -n In Verzeichnis ~/
    read tdir
    mkdir -p ~/${tdir}
    for i in $@; do
    	if [ -e ${sdir}/${i} ]; then
		    cp ${sdir}/${i} ~/${tdir}
    	else
    		echo "Nicht existierende Datei: ${sdir}/${i}"
    	fi
    done

8.2
Schreiben Sie eine Prozedur, die von allen gewöhnlichen Dateien eines der Prozedur als Parameter zu übergebenden Textverzeichnisses die Anzahl der Wörter ermittelt und die Dateinamen sowie (d.h. gefolgt von ...) die jeweilige Wortanzahl zeilenweise in eine einzurichtende Datei "Wortanalyse" schreibt. Der Name des Textverzeichnisses sei relativ zum aktuellen Verzeichnis.
Hinweis: Verwenden Sie das find-Kommando!

    #!/bin/sh
    ofile=Wortanalyse
    #dosnt work as it gives wc first followed by filename:
    #find $1 -xtype f -exec wc -w {} +
    echo -n > ./${ofile}
    if [ ! -d ./$1 ]; then
    	echo "Fehler! Verzeichnis existiert nicht: ./$1";
    	exit 1;
    fi
    for i in $(find ./$1 -xtype f); do
    	echo -n "${i} " >> ./${ofile}
    	cat ${i} | wc -w  >> ./${ofile}
    done

8.3
Schreiben Sie eine Prozedur del, der beliebig viele Namen von zu löschenden Dateien (Dateinamen relativ zum aktuellen Verzeichnis) als Parameter übergeben werden sollen. Die Prozedur soll die Dateien aber nicht endgültig löschen, sondern in ein vorher im Homedirectory einzurichtendes Verzeichnis .muell übertragen.
Nach mehrmaliger Benutzung der Prozedur soll dieses Verzeichnis je nach Bedarf (z.B. am Sitzungsende) mit dem üblichen UNIX-Kommando rm geleert werden. Die Dialog-Abfrage, ob das Verzeichnis geleert werden soll, soll über ein kleines Menü erfolgen.

    #!/bin/sh
    if [ $# -eq 0 ]; then
        echo "Keine Parameter angegeben!"
        exit 1
    fi
    recycle=~/.muell
    mkdir -p ${recycle}
    echo "Loeschen der Dateien: $@"
    for i in $@; do
        if [ ${i} = "." -o  ${i} = ".." ]; then continue; fi
        echo "... loesche ${i}"
        mv ${i} ${recycle}
    done
    echo -n "Soll der Muell geleert werden? (j/N) "
    read answer
    if [ ${answer} = "j" -o ${answer} = "y" -o ${answer} = "J" -o ${answer} = "Y" ]; then
        rm -d -I -- ${recycle}/*
    fi

9.1
Erzeugen Sie in Ihrem Heimat-Verzeichnis eine eigene bzw. erweitern Sie die bereits vorhandene .profile-Datei in der Weise, daß ein kurzer Begrüßungstext erzeugt und das Bereitschaftssymbol auf Ihre Initialen, gefolgt von einer spitzen Klammer eingestellt wird.
Sorgen Sie weiterhin dafür, daß Ihre neu angelegten Dateien alle Zugriffsrechte nur für Sie selbst erhalten. 

    #begruessungstext
    echo "albern aber gefordert: ein kurzer Begruessungstext"
    #initialen mit gnu textutils raussuchen, weil setzen oder awk ist langweilig
    mn=$(finger -s $USER|tac|head -1|tr -s [:blank:] ' ')
    fn=$(echo $mn|cut -d' ' -f3)
    cn=$(echo $mn|cut -d' ' -f2)
    initials=$(echo $cn|cut -c1)
    initials=${initials}$(echo $fn|cut -c1)
    PS1=${initials}\>
    export PS1
    #zugriffsrechte einschränken
    umask 077


9.2
Arbeiten Sie am Rechner iaix1.informatik das Programm lprog ab, das als Lademodul im Verzeichnis /glb/studi steht, und speichern Sie das Ergebnis in eine Datei mit dem Namen resultlprog in Ihrem text-Verzeichnis.
Hinweis: Fügen Sie einen neuen Kommandosuchpfad ein.

	Die Architektur wird im RZ nicht mehr bereitgestellt. Die Aufgabe ist daher ohne Gegenstand.

10.1
Richten Sie in Ihrem Home-Directory eine Verbindung zum Verzeichnis /glb/studi ein. 

	ln -s /glb/studi ~/glb-studi

10.2
Übertragen Sie aus der Datei stadt, die im Verzeichnis /glb/studi zu finden ist und alle deutschen Großstädte enthält, den Eintrag mit Ihrer Landeshauptstadt in Ihr eigenes Verzeichnis. 

    cat glb-studi/stadt |grep Dresden > ~/stadt

10.3
Ermitteln Sie, wieviele Großstädte das Land Baden-Wuerttemberg hat. 

    cat glb-studi/stadt |grep Baden-Wuerttemberg|sort|uniq|wc -l

10.4
Schreiben Sie eine Funktion zur bequemeren Handhabung des find-Kommandos! 

    #!/bin/sh
    set -x
    #suche eine datei im home-dir bzw. einem angegeben verzeichnis
    hdir=~
    datei=$1
    #Parameter vorhanden?
    if [ $# -eq 0 ]; then
        echo "Sie haben keine Parameter angegeben"
        exit 1
    fi
    if [ $# -gt 2 ]; then
        echo "Sie haben zuviele (mehr als zwei) Parameter angegeben"
        exit 1
    fi
    if [ $# -eq 2 -a -d "$2" ]; then
        hdir=$2
    fi
    find ${hdir} -name ${datei}


10.5
Ermitteln Sie die Unterschiede zwischen den Programmen unix.f und hunix.f (beide im Verzeichnis /glb/studi) und gleichen Sie unix.f an hunix.f an (in Ihrem text-Verzeichnis). Geben Sie eine Kommandoprozedur an, die die Namen von zwei Texttadeien als Parameter übergeben bekommt und die erste Datei automatisch an die zweite angleicht. 

    #!/bin/sh
    # angleichen von zwei dateien, sodass kein Unterschied besteht heißt soviel wie eine Kopie erstellen
    # unterschied feststellbar mit:
    #diff /glb/studi/unix.f /glb/studi/hunix.f
    # angleichen mit
    #cp $2 $1
    #"mkeq"
    if [ $# -ne 2 ]; then
        echo "Sie benötigen zwei Dateien als Parameter!"
        exit 1
    fi
    if [ ! -f "$1" -o ! -f "$2" ]; then
        echo "Eine der angegeben Dateien existiert nicht oder ist keine einfache Datei!"
        exit 1
    fi
    diff $1 $2 >/dev/null
    if [ $? -eq 0 ]; then
        echo "Warnung: Die Dateien sind schon gleich!"
        exit 1
    fi
    #cp $2 $1
    diff -e $1 $2 > /tmp/mydiff
    echo "w" >>  /tmp/mydiff
    echo "q" >>  /tmp/mydiff
    ed -s $1 < /tmp/mydiff

10.6
Schreiben Sie eine Prozedur, die alle die Dateien Ihres Home-Directories (einschließlich aller Unterverzeichnisse) anzeigt, die in den letzten n Tagen (Anzahl n als Parameter übergeben!) modifiziert worden sind. 

    #!/bin/sh
    n=3
    if [ $# -gt 1 ]; then
        echo "Warnung: Mehr als ein Parameter werden ignoriert!"
    fi
    if [ ! $# -ge 1 ]; then
        echo "Warnung: Der Parameter n fue die Anzahl der Tage wurde nicht angegeben! (Fallback auf n=${n})"
    else
        n=$(expr $1 \* 1) >/dev/null
        if [ $? -ne 0 ]; then
            echo "Fehler: Der Parameter1 ist keine Zahl! ($1)"
            exit 1
        fi
    fi
    find ~/ -mtime -${n} #-exec ls -ld {} +

11.1
Schreiben Sie eine Prozedur, die ein Programm mit längerer Laufzeit im Hintergrund zur Abarbeitung bringt. Der Programmname soll als Parameter übergeben oder im Dialog abgefragt werden.
Das Programm soll außerdem die Eigenschaft haben, daß es seine Ergebnisse in verschlüsselter Form erzeugt. Den Klartext erhält man, wenn man folgende Zeichentransformationen vornimmt:

7 --> f     Q --> e     X --> r
Y --> l     Z --> i     J --> u     3 --> g

Testen Sie Ihre Prozedur anhand des Programms prog.b5, das im Verzeichnis /glb/studi steht. 

	$t=/tmp/results-file
	if [ ( $# -ge 1 ) -a ( -f $1 ) ]; then
	  $1 > $t &
      cat $t |tr 7 f|tr Q e|tr X r|tr Y l|tr Z i|tr J u|tr 3 g
	fi

11.2
Stellen Sie eine Prozedur auf, die Ihnen jede Minute einmal die Uhrzeit anzeigt; aktivieren Sie sie zuerst im Vordergrund und dann im Hintergrund. Beenden Sie die Prozedur vor Sitzungsende wieder. 

    date |cut -d" " -f4

12.1
Entwickeln Sie eine Kommandoprozedur, mit der Sie den Wochentag Ihrer Geburt ermitteln können! Der Prozedur sind beim Aufruf in der Kommandozeile Tag, Monat und Jahr des Geburtsdatums jeweils als Zahl und in dieser Reihenfolge zu übergeben. Der Wochentag soll auf stdout ausgegenen werden. Beispiel:

        ./proz12_1 15 3 1921
         Dienstag
         
12.2
Im Verzeichnis /glb/studi finden Sie die Dateien kunden, aktikel und auftraege mit Datensätzen, die folgendermaßen aufgebaut sind:

kunden: Kundenname Kundennummer Wohnort
artikel: Artikelname Artikelnummer Preis
auftraege: Kundennummer Auftragsnummer Artikelnummer Stückzahl Liefertermin

Sortieren Sie
a) die Datei kunden alphabetisch nach Namen

    sort -k1,2 /glb/studi/kunden #> ~/kunden_byName

b) die Datei artikel nach Artikelnummern

    sort -k3 /glb/studi/artikel #> ~/artikel_byArtikelNummer

c) die Datei auftraege erstens (erstrangig) nach Kundennummern und zweitens (zweitrangig) nach dem Liefertermin

    sort -k 1,1 -k 5,5 /glb/studi/auftraege > ~/auftraege_byKundennrAndLiefertermin

12.3
Stellen Sie eine Datei kundenauftraege für alle Kunden auf, die einen Auftrag erteilt haben. Sie enthalte: Kundennr., Name, Ort, Artikelnr., Preis, Stückzahl und Auftragsnr. 

	#Kundennr., Name(2), Ort, Artikelnr., Preis, Stückzahl und Auftragsnr. 
    sort -k 3,3 /glb/studi/kunden > kunden_byKundennr
    #Artikelname Artikelnummer Preis
    sort -k 2,2 /glb/studi/artikel > artikel_byArtikelnr
    sort -k 3,3 /glb/studi/auftraege > artikel_byArtikelnr
    #Kundennummer Artikelnummer Preis Stückzahl Auftragsnummer
    join --check-order -j1 2 -j2 3 -o 2.1 1.2 1.3 2.4 2.2 artikel_byArtikelnr auftraege_byArtikelnr | sort -k 1|uniq > auftraege_byKundennr
    #Kundennummer Kundenname(2) Wohnort Artikelnummer Preis Stückzahl Auftragsnummer
    join --check-order -j1 3 -j2 1 -o 2.1 1.1 1.2 1.4 2.2 2.3 2.4 2.5  kunden_byKundennr auftraege_byKundennr > kundenauftraege

12.4
Sortieren Sie die Datei kundenauftraege
a) für den Versandt nach Orten,

    sort -k 4 kundenauftraege

b) für die Abrechnung nach Kunden

    sort -k 2 kundenauftraege

c) für die Lagerhaltung nach Artikelnummern.

    sort -k 5 kundenauftraege

12.5
Ordnen Sie die Datei Kundenaufträge so, daß bei

(a) die Ortsnamen, 

    awk '{print $4" "$1" "$2" "$3" "$5" "$6" "$7" "$8}' kundenauftraege

(b) die Kundennamen und 

    awk '{print $2" "$3" "$4" "$1" "$5" "$6" "$7" "$8}' kundenauftraege

(c) die Artikelnummern

    awk '{print $5" "$2" "$3" "$4" "$1" "$6" "$7" "$8}' kundenauftraege

zuerst ausgegeben werden. 

12.6
Die Datei stadt im Verzeichnis /glb/studi enthält alle deutschen Großstädte. Ermitteln Sie daraus die Städte mit mehr als einer halben Million Einwohner. Geben Sie die gefundenen Datensätze in der Reihenfolge
Einwohnerzahl - Städtename - Bundesland
aus und sortieren Sie die Städte der Größe nach! 

    awk '{ if($2>500000) print $2" - "$1" - "$3}' /glb/studi/stadt |sort -t- -d -r -k 1
