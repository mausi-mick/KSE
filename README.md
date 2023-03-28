# KSE
Kennlinienschreiber mit Encoder
.
   Kleiner , feiner  Kennlinienschreiber


Wozu braucht man einen Kennlinienschreiber ?

Reicht nicht ein Transistortester oder ein Voltmeter?
Für einen ersten Check ist ein Transistortester sehr hilfreich, zumal er auch den Typ des Transistors  und die Pin-Belegung ermitteln und anzeigen kann.
t. 
Aber die Ausgabewerte beziehen sich dabei nur auf einen Arbeitspunkt (Kollektorstrom).
Für den Schaltungsentwickler ist es aber oft wichtig, wie sich das Bauteil an verschiedenen
Arbeitspunkten – z.B. Basisströmen – verhält.

Worin unterscheidet sich der hier vorgestellte Kennlineinschreiber von anderen?

    • kompakter Aufbau:                                                                                                 Abmessungen: 8x9x6cm (lxbxh),  Gewicht: < 500 g (inkl. 18650 Lio-Akku).
    • unabhängig von externer Stromversorgung (Akku Betrieb,mit einer 18650 Zelle)
    • ausgelegt für bipolar Transistoren/Dioden, MosFets, J-Fets(incl. Depl.Mode MosFets).
    • Kollektor/Emitterspannung < 12V , 0 ..150mA  optimal für Kleinleistungstransistoren
    • Basisstrom von ca 5µA ... 1000µA mit präziser Konstant-Stromquelle, 
    • Gatespannung  100 mV ...12 V in 100 mV Schritten (änderbar), 
    • einstellbare Strombegrenzung (z.B. Dioden 5mA, 50mA,…)
    • relativ grosses Farbdisplay (3.2“)
    • einfache und schnelle Eingabe über Dreh-Encoder an Interrupt-Pins
    • hohe Genauigkeit und klare Kennlinien dank 12 bit ADC’s / DAC’s
    • diverse Skalen für x (U) und y-(I) Achse : detaillierte Darstellung interessanter Bereiche
    • Direkter Vergleich von Kennlinien (Typen gleicher Polarität oder Komplementärtyp)
    • Anzeige von typischen Werten wie Beta(Ib), V-Threshold,V-Pinch, Idss
    • Bluetooth Schnittstelle (BLE 4.0) für Datentransfer zum PC bzw. Smartphone/ Tablet
    • Datentransfer über USB zur Darstellung der Kurven auf PC mit z.B. Processing.
    • Speichern der Eingabeparameter im internen EEPROM
    • geringer Stromverbrauch: Uce (15V) erst bei Messvorgang zugeschaltet. 
    • Zusatzfunktion: einstellbare  Konstantspannungsquelle 1 mV  bis 12, 00V,> 10 mA
    • Zusatzfunktion: einstellbare Konstanstromquelle von 1µA bis 4000 µA
    • Zusatzfunktion: Treppenstufengenerator für niedrige Frequenzen +4V, ...0V (...-4V) 
    • untergebracht auf 5 Platinen (Kicad) einlagig 
    • relativ niedrige Bauteilkosten

Dieser Kennlinienschreiber basiert auf dem sehr interessanten und gut dokumentierten Projekt von Peter Balch : Transistor Curve Tracer [1], aus dem auch u.a. die Grafik-library leicht modifiziert übernommen wurde.

Ich habe mich dann aber doch ziemlich  - auch vom Konzept her - weit davon entfernt, um eine bessere und rauschärmere Darstellung hinzubekommen mit einem deutlich erweiterten Messbereich ,einer einfachen Bedienung und erweiterter Funktionalität, was sich leider auch im Preis niederschlägt. 
Ich habe die bisherige Bedienung über das Touchpanel eliminiert und mache die Programmsteuerung und Parameteränderungen über zwei Drehencoder, die an den beiden ext. Interrupt-Pins des NANO hängen und so eine schnelle Reaktion ermöglichen. Die Eingabe ist hiermit – gerade bei dem kleinen Display und den vielen Parameterwerten – wesentlich angenehmer und schneller als per Touch. 

Der Nachbau ist wegen einiger SMDs  und der Verdrahtung einiger Platinen  komplexer und daher nicht unbedingt für „Anfänger“ geeignet. Bei einigen Spezial-ICs ist/war auch mit erheblichen Beschaffungszeiten (in Deutschland) zu rechnen.

Basis ist ein Arduino-NANO(Clone), am besten mit vorinstalliertem Opti-Boot Loader [2,3,4], da man dort 1.5 kB mehr freien Flash-Speicher hat, ansonsten hat man nur ca 50 Byte Flash frei.
(ATMega328 Board mit vorinstalliertem Opti-Boot-Loader: z.B. Arduino-UNO,Adafruit Metro Mini 328,  oder ein Board mit mehr Flash und Port-Pins (für Erweiterungen): ATMega2560 ).
(Anmerkung:
Auch das UNO-Board oder Adafruit Metro-Mini sind hier verwendbar, da ich die zusätzlichen rein analogen Pins des NANO (A6,A7) nicht benutze).

Das Adafruit-Board Metro Mini 328 hat neben der besseren Anordnung der Pins(alle digit.Pins und somit auch Pin13 (SPI-SCL)  auf einer Seite) auch die Möglichkeit, durch eine Lötbrücke die Pegel auf 3.3V zu setzen, sodass man für das Display und das HM-10 BLE-Modul keine Spannungsteiler zur Pegelanpassung benötigt.

Der Mikroprozessor steuert zwei 12bit-DACs (im MCP4822 SPI) für die Uce Versorgung , ausserdem für die Basis/Gate-Ansteuerung einen 12 bit DAC (MCP4725-I2C) an, der über einen DC/DC-Wandler (5 V → 30V) und für den I2C Bus über einen ADUM1250/51 isoliert betrieben wird.  

Dieser total isolierte DAC (Bereich von 1µA bis 4mA, 1µA Schritte, hier nur von 5µA bis 1200µA genutzt für die bipol. Transistoren genutzt) erzeugt den Basisstrom bzw. die Gate-Spannung in der – zum Bauteil / „device“ - passenden Polarität über eine Präzisionskonstantstromquelle.
 
Zugeschaltet wird der Basisstrom (bzw. die Gatespannung)  über elekronische Schalter („Photomos-Relais“) mit niedrigem On-Widerstand und Leckstrom (z.B. TLP222A ).

Neben diesen DACs wird auch ein 12-bit ADC (MCP3402 SPI) zur Messung der Collector-EmitterSpannung und zur am Messwiderstand RS (50 Ohm) abfallenden Spannung (zur Ermittlung des Collector-Stroms) verwandt. Er dient auch zur Überwachung der Akku-Spannung.
Momentan werden nur 3 von den 4 Eingängen des ADC’s genutzt.

Ausserdem versorgt der NANO das bis zu 3.2 Zoll grosse Farb-Display (ILI9341) (an SPI-Bus).

Probleme gab es mit den 4 SPI-Devices wegen der unterschiedlichen Geschwindigkeit, da das Display mit 8 MHz läuft, die DAC’s mit 3.6 MHz und der ADC aber nur mit 2 MHz                      (und der Touch noch langsamer, wird aber in dieser Version nicht genutzt).

Als realisierbare / nicht ganz perfekte Lösung speichere ich maximal 320-Messwerte pro eingestelltem Basisstrom in einer Tabelle mit SPI-Speed 2 MHz ab.
Anschliessend erhöhe ich die SPI-Speed auf 8MHz für das Display und
 gebe die Tabellenwerte auf dem Display aus, um dann wieder – mit reduzierter SPI-Speed - die nächsten 320 Messwerte zum nächsten Basisstrom zu speichern.

Die Spannung Uc wird über die beiden DACs des MCP4822 erzeugt, wobei DAC-B nur
die Werte (0V, 2V,4V ...10V) am Opamp TCA0372 ausgibt,
DAC-A erzeugt die Zwischenwerte von 0.0mV, 0.5mV, 1.0mV.,..2000mV.
Sie werden dann zu den aus DC-B erzeugten Werten addiert (zwei invertierende Verstärker hintereinandergeschaltet) , sodass insgesamt der Bereich von
0V bis 12V in 0.5mV – Schritten mit sehr guter Linearität erzeugt werden kann.
Diese Auflösung ist zwar etwas übertrieben, bei kleinen Spannungswerten (z.B. Kennlinie von Shottky-Dioden oder im Anfangsbereich der Kurven von bipolar bzw. Feldeffekttransistoren) bringt das aber was, auch wird durch „Oversampling“ der Mittelwert der Messungen zum gleichen x_Wert gebildet

Der Summierverstärker TCA0372 mit Verstärkung +1 für DAC_A und Verstärkung +10 für DAC_B ist in Abbildung 1 skizziert.
Da der TCA0372 kein Rail-to Rail Vertärker ist, benötigt er eine negative Hilfsspannung: 
 aus den +15V wird mithilfe eines kleinen Spannungswandlers (LMO78-5-.5 )  -5V erzeugt. 
Damit erhält man auch in Nullpunktsnähe saubere Kurven, gerade die 1, 2, 3V Skalen profitieren davon.

Stromversorgung

Versorgt wird der Kennlinienschreiber aus einer einzelnen Lion-Akku-Zelle (18650), die Spannung für das NANO-Board  (7.5 V) wird mit einem Aufwärtswandler (MT3608) erzeugt. Ein zweiter MT3608 dient zur Versorgung des TCA0372-Verstärkerboards mit 15V.
Der MT3608 hat den Vorteil, dass er ab ca 2,5V funktioniert (wichtig, da nur eine Lion-Zelle
benutzt wird (ca3V …. 4,2V). Ausserdem arbeitet er mit ca 1.2 MHz, sodass bei den niedrigen Frequenzen wenig Störungen auftreten.

Etwas kritisch ist seine Einstellung/Inbetriebnahme, da der Schleifer des 10-Gang-Trimmers direkt mit dem FB(feedback-Pin 3)  verbunden ist und am oberen Anschlag die Ausgansspannung direkt mit FB  verbunden ist, der wohl nur 0.6V ? verträgt.
Man sollte also vor Anlegen der Eingansspannung den Regler mit ein paar Umdrehungen aus der oberenAnschlagsposition bringen (und zwar nach höheren Ausgansspannungswerten , d.h. nach links drehen   chinesische Logik ??)   [5,6],

Um Energie zu sparen, wird die 15V Versorgung erst aktiviert, wenn die Kurven aufbereitet werden.
Die 7.5V stehen nach Einschalten für das Microprocessor Board gleich zur Verfügung, an dem u.a.  das Display und die Konstanstromquelle hängen, die aber weniger al 1W benötigt.
Das NANO Board benötigt incl. Display ca 400 mA im Betrieb, beim Einschalten aber über 1 A.

Das Adafruit Board braucht wegen eines bessern Reglers bei 3.3V deutlich weniger, auch reichen hier ca 6V am Eingang.

Nach diesem technischen Überblick mal etrwas mehr zum Test-Ablauf:

Nach dem Einschalten erscheint auf dem Display der Startbildschirm , auf dem an der linken Seite nur drei grosse Icons erscheinen mit Auswahlfeldern (Buttons) für:
    •  bipolar Transistoren (inclusive Dioden)
    •  Mosfets
    •  Junction-Fets (incl. n-Depletion-Mode-Mosfets)
      
die über einen Drehencoder angesteuert werden.
Die Aktivierung einer Schaltfläche erfolgt durch Drehen des 2. Encoders.
Daraufhin werden im oberen Bereich zwei Schaltflächen („P“ bzw „N“) zum Starten des Messvorgangs angezeigt, vorher sollte man aber die unterhalb in zwei Spalten dargestellten Parameter begutachten  und eventuell modifizieren.
Im mittleren Display Bereich die Parameter: 

    •  min:   minimaler Wert (Basisstrom bzw. Gatespannung), mit dem der Messvorgang gestartet werden soll
    •  incr: Schrittweite, mit der Basisstrom bzw. Gatespannung erhöht werden soll
    •  max:  maximal datzustellende  Basisstrom bzw. Gatespannungs-Kurve

Die Basisströme werden in µA eingetragen, die Gatespannung in 100mV Schritten.
 Von einer Diode wird dann ausgegangen, wenn bei  „bipolar“ das Feld  incr: auf 0 gesetzt wird.

Am linken Rand des Displays werden folgende Parameter angezeigt:

    •   Bluet/CCS: Transfer der Daten mit BLE 4.0 an PC bzw Smartphone, bzw Kostantstrom/Konstantspannungs-Betrieb
    •   x-scale[V]:    Länge der x-Skala  ( 1V,  2V,  3V,  4V,  6V,  12V)
    •   y-scale[mA]  Höhe   der y-Skala ( 2 mA, 5 mA, 10 mA, 20 mA, 50 mA, 100 mA)
Anmerkung: 
 Feld Bluet/CCS = 1       Ausgabe der Kennliniendaten über Bluetooth an Smartphone 
                           = 2,...6  Umschalten auf Konstantspannungsquelle, Konstantstromquelle bzw. 
                                        Treppenestufengenerator. 
Die Verwaltung der Parameter erfolgt ähnlich wie beim Startbildschirm beschrieben, zwischen den Feldern springt man durch Drehen des Encoders 2, Auswahl eines Paramters durch mit dem anderen Encoder 1, ändern der Parameter duch Encoder-Drehung, Speichern/Verlassen des Parameters durch Encoder 2.

Ist man mit den Einstellungen zufrieden, startet man den Messvorgang / Kurven mit Drehen des anderen Encoders: „P“  (für p-Devices) bzw. “N“ (für n-Devices) r 
Hier hat man eine geringe Wartezeit zum aktivieren, in der man nochmals die Parameter prüfen sollte. In dieser Wartephase blinkem die aktivierten Felder P  bzw N.

Erst jetzt sieht man die ausgewählten Skalen und im oberen Bereich das ausgewählte Device inclusive Icon.

Die Kurven werden vom x-0-Punkt aufgebaut und zusätzliche Daten (wie z.B. beta/hfe beim npn/pnp und der Basisstrom/Gatespannung )  am Ende der Kurven eingetragen.

Am Ende der Messung bleibt der Bildschirm mit den dargestellten Kurve stehen und es erscheint links oben eine Anfrage / Aufforderung,


    •   ob man die Messung beenden will  („Stop ?“)
    •   ob man die Messung fortsetzen will mit geänderten Parametern  („Param ?“)
    •   ob man das gerade dargestellte Bauteil mit einem Bauteil gleicher Polarität vergleichen will („Compare ?“) 
    •   ob man das gerade dargestellete Bauteil mit einem Bauteil anderer Polarität vergleichen will („Complem. ?“)  (komplementär-Typ z.B. antiparallele Diode)


Anmerkung:

Will man z.B. die Kennlinien von einem n-Device mit einem p-Device vergleichen, ermittelt man zuerst normal die Kennlinien des n-Devices, tauscht das n gegen das p-Device in der Textool Messfassung und startet den Vergleich durch Auswahl des Feldes „Complementary“ mit Encoder und Aktivierung mit Taster.
Sofort werden die Kennlinien des p-Devices zusätzlich in einer anderen Farbe auf dem Display dargestellt („Komplementärtyp“).


Vergleich von Komponenten gleicher Polarität erfolgt entsprechend durch Auswahl des Feldes „Compare“.

   Speicherung geänderter Parameter-im internen EEPROM

Die festgelegten Parameter werden beim Start der Kurven im internen EEPROM hinterlegt , 
sodass man auch beim Einschalten des Geräts die letzt eingegebenen Parameter angezeigt bekommt. 

Im EEPROM sind dafür folgende Adressen belegt:

    •   0: Transistor-Typ  0: bipolar,
    •   1: Bluetooth : 1=on, 0=off
    •   2: x-Skala: 1,2,3,4,6,12
    •   3 y-Skala: 2,5,10,20,50,100 mA
    •   4: minimaler Basisstrom bipolar-Transistor
    •   6: maximaler Basisstrom bipolar-Transistor 
    •   8: increment Basisstrom bipolar-Transistor 
    • 10: minimale Stepweite für Eingabe  min: ( Feld 4 )
    • 12: minimale StepWeite für Eingabe max: ( Feld 6 )
    • 14: minimale StepWeite für Eingabe incr:: ( Feld 8 )

    •   20: Transistor-Typ  1:  Mosfet
    •   21: Bluetooth : 1=on, 0=off
    •   22: x-Skala: 1,2,3,4,6,12
    •   23 y-Skala: 2,5,10,20,50,100 mA
    •   24: minimaler Gatespannung Mosfet
    •   26: maximaler GatespannungMosfet 
    •   28: increment Gatespannung Mosfet 
    •   30: minimale Stepweite für Eingabe  min: ( Feld 24 )
    •   32: minimale StepWeite für Eingabe max: ( Feld 26 )
    •   34: minimale StepWeite für Eingabe incr:: ( Feld 28 )

    •   40: Transistor-Typ  2:  J-Fet 
    •   41: Bluetooth : 1=on, 0=off
    •   42: x-Skala: 1,2,3,4,6,12
    •   43 y-Skala: 2,5,10,20,50,100 mA
    •   44: minimaler  Gatespannung J-Fet
    •   46: maximaler Gatespannung-J-Fet 
    •   48: increment  Gatespannung J-Fet 
    •   50: minimale Stepweite für Eingabe  min: ( Feld 44 )
    •   52: minimale StepWeite für Eingabe max: ( Feld 46 )
    •   54: minimale StepWeite für Eingabe incr:: ( Feld 48 )

Ausserdem kann man den Bauteil-Namen (z.B. BC548C  oder auch andere Informationen) im internen EEProm ab Adresse 60 hinterlegen: 

    •   60: Länge des Textfeldes 
    •   62  Text 1. Byte
    •   63  Text 2. Byte
    •  ...
 das Textfeld wird im Display unterhalb der P / N Buttons angezeigt und ist über einen Encoder basierten kleinen Editor verwaltbar – maximal 11 Einträge.
: 
   
 Datentransfer  über Bluetooth (BLE 4.0)

In das Gerät ist ein HM-10 Module integriert mit BLE 4.0, es ist über .Wippschalter mit der seriellen Schniitstelle RX / TX und damit mit USB verbunden. 
Die Aktivierung des Transfers erfolgt im Auswahl-Menü rechts oben unterhalb „N“-Button.
Bluet./CCS:  1   (0 für  Blutetooth nicht aktiviert)
Folgende Werte werden mit Separator Linefeed (“\n“ ) übertragen:

    • X:  x Skala und nachflolgend 1,2,3,4,6 oder 12 die SpannungsEndwerte der x-Achse
    • Y:  y-Skala und nachfolgend 2,5,10,20,50,100 die StromEndwerte der y-Achse
    • P  bzw. N : die Polarität (npn/pnp,nMos,pMos,nJFet,pJFet)
    • T, M, F (D) : der Typ (Transistor(bipolar), Mosfet, jFet (Diode noch nicht real.)
    • C: Kurve pro Basisstrom bzw. Gatespannung     hier werden jetzt bis zu 320 Werte der y- Achse  - durch Komma getrennt  -  ausgegeben
    • Q: Ende der Kurve
    • B:  bzw. G : für Basisstrom bzw. Gatespannung
    • E : Error Fehlermeldung bei Überschreitung des maximalen Collector-Stroms
    • H : hfe/beta von bipolar-Transistoren in Abhängigkeit vom Basisstrom (am Ende der Kurve)
    • O: Ohm (bei j_Fet mit Skala 1V ermittelt bei Uds =0.5V)
    • D : Diodenstrom bei Ud in mA (Diode wird „erkannt“, wenn bei npn/pnp incr=0)
    • U : Diodenspannung Ud bei Idiode in mV (Fluss bzw. Sperrspannung bei Zener < 12V)
    • Z : Ende der Kurve

Anmerkung  Bei ausgeschaltetem HM-010 Modul (BLE 4.0) können in Stellung  Bluet./CCS = 1
obige Parameter über USB an den PC übertragen werden und z.B. mit TeraTerm überwacht werden.

Verwendung als präzise KonstantSpannungsquelle

Der Modus:  Konstantspannungsquelle wird mit  Bluet./CCS = 2  aktiviert :

hier kann man bei dem Parameter  min.incr und max:  den den Spannungswert
bis maximal 12 V angeben. 

Verwendung als präzise Konstantstromquelle

Der Modus:  Konstantstromquelle wird mit  Bluet./CCS = 3  aktiviert :

hier kann man bei dem Parameter  max:  den den Konstanstrom in µA (1...4000) angeben. 






Verwendung als Treppenstufengenerator

Der Modus:  Treppenstufengenerator wird mit  Bluet./CCS  >= 4  aktiviert :

    • 4:  nur Stromwerte steigen von min::  µA bis max:  µA mit Schrittweite incr: µA
    • 5:  Stromwerte steigen und fallen wie bei 4:  
    • 6:  Stromwerte steigen und fallen wie bei 4:  mit Umschaltung Strom in negative Richtung (Treppenkarven von  maximal +4V … 0   -4V )
Die Verweildauer pro Stufe in µs wird über die Parameter x-scale und y-scale festgelegt.

Die Frequenz ergibt sich aus der Verweildauer und der über die Parameter-
 min:,  incr:  max:  festgelegten Stufenanzahl.


   Erweiterungsmöglichkleiten / Ausblick

    • grösserer Spannungs- / Strombereich Uce/Ic
Für manche Anwendungen ist der Spannungsbereich (Uce <= 12V)-
 bzw. Strombereich ( <100mA ) nicht ausreichend.
  Ein Betrieb mit bis zu 24V (der DC/DC-Wandler liefert maximal 28V) ist ohne HW-Änderungen möglich, allerdings zu Lasten des Stromverbrauchs , da sollte man das Gerät auf Betrieb mit 2 oder mehr Lion-Zellen umbauen oder den NANO mit 3.3V/3.6V betreiben (Umbauanleitungen im Netz, ab 3.6 V sicherer Betrieb des ATMega328P mit 20 MHz ). Da geht dann der Stromverbrauch im standby drastisch runter und man spart sich die Pegelanpassung für das TFT-Display. Ich hab aber die SPI-Schnittstellen zu den DACs und ADCs (auf dem TC0372-Board) bei 3.6V nicht getestet.
  Ein höherer Strombereich ist kritischer, da dann u.a. auch der Schaltregler an seine Grenzen kommt und der TCA0372 gekühlt werden müsste.

    • grösserer Strom Konstantstromquelle / CCS
   bis ca 10 mA leicht durch Ersetzen des Messwiderstandes  (250 Ohm) der CCS auf z.B 100 Ohm.
   Oberhalb muss man wohl u.a. auch den DC/DC-Wandler (1W/30V) wechseln und das Platinenlayout und den Mosfet entsprechend anpassen.

    • Schnittstelle zum Transitortester( „TT“):

            automatische Erkennung des Bauteils inclusive Pin-Zuordnung. 
Das ist mit dem NANO mit der jetzt integrierten Funktionalität nicht möglich, da der Flashspeicher bereits zu 99% belegt ist. Man müsste dann auf einen anderen Microprozessor zurückgreifen, z.B. den ATMega2560, der dazu auch noch eine Menge Port-Pins zur Verfügung stellt. 
Ich hab das mal – in einer einfacheren Version – mit einem NANO (+ Portexpander MCP23017 )  realisiert  [7],[8] : 
-   Erweiterung TT um serielle Schnittstelle( SoftwareSerial) und Übertragen
         Transistortyp/Polarität,Belegung Pin1,2,3 ...
  - Verlegung (Duplizierung)  der Textool-Pins des TT s in eine 3x3-Matrix Schaltbox,
 -  Einbau Serielle Schnittstelle in Kennlinienschreiber zum Lesen der Daten vom TT .
 -.. Starten Kennlineinschreiber mit den Steuerungs-/Schalt-Daten vom TT .
Das hat damals – mit erheblichen Schaltungsaufwand – gut funktioniert.

    • Speichern auf SD-Karte 
  Speichern auf SD-Karte ist nur mit „grösserem“ Prozessor (ATMega2560) siehe oben möglich,
  da die Bibliotheken viel Platz benötigen .

   Hardware-Komponenten

hier sind jetzt nur die wesenlichen Komponenten / Module aufgeführt, die Details kann man den KiCad Stücklisten entnehmen.

    • ILI9341 SPI-Display (3.2“) (Touch Version nicht nötig)
    • Arduino NANO bzw. Clone (möglichst mit OptiBoot-Bootloader)
    • MCPM4822 Dual-DAC 12 bit SPI
    • MCP3204-Quad-ADC 12 bit SPI
    • MCP4725DAC 12 bit I2C
    • ADuM1250/1 ISOLATOR I2C 
    • DC/DC RH0515 1W 5V to 30V 
    • TCA0372 Dual Power Amplifier
    • LMO78-05.05  +7..+24V to – 5V
    • Textool Fassung
    • Dreh-Encoder (2 Stück)
    • 10 x PhotoMosrelais (z.B. TLP222A)  
    • 2 x MT3608 boards ( 3 V..4.2V --> 7,5V bzw. --->15 V)
    • Lion-Akku incl. Halterung
    • Lademodul Lion-Akku
    • HM-10 Module Bluetooth 4.0


      für diese Komponenten hab ichetwa  80  € ausgegeben, momentan ist aber mit höheren
      Preisen zu rechnen.


[1] https://www.instructables.com/Transistor-Curve-Tracer/
[2] GitHub - Optiboot/optiboot: Small and Fast Bootloader for Arduino and other Atmel AVR chips 
[3]  Neusten Bootloader flashen auf Arduino Nano China clone – Thing King – Technik & DIY (thing-king.de)
[4] How do I install optiboot on the newest arduino IDE? - Using Arduino / Programming Questions - Arduino Forum 
[5] https://chips-and-more.com/cm/pdf/K3608.pdf
[6] https://datasheetspdf.com/pdf-file/909246/AEROSEMI/MT3608/1
[7] https://www.youtube.com/watch?v=7WQflcy4CrQ
[8] https://www.youtube.com/watch?v=ybFWR0rq9jg


       Vergleich n-JFet BF256B mit p-JFet J176 Skala 6V 10mA
[9] https://www.youtube.com/shorts/95HiBvJB_0A
       NPN Skala: 2V, 5mA Parameter über Dreh-Encoder
[10] https://www.youtube.com/shorts/zspOeL5-aKM
       Kennlinienschreiber mit Präzisionskonstantstromquelle
[11] https://www.youtube.com/watch?v=6bx0Xuingm4
       Vergleich n-JFet BF256B mit p-JFet J176 Skala 1V 5mA
[12] https://www.youtube.com/shorts/LGOkdHCgLTI
        npn Test mit incr. 2,4,10,20 µA, Skala 4V 5mA
[13] https://www.youtube.com/watch?v=azDgB1QcklA&t=22s
       NPN Basistrom 5µA ... 240µA
[14] https://www.youtube.com/shorts/C6GhPL9V9Fg
      Kennlinienschreiber als bipolare Konstanstromqelle : Bereich 0 µA ... +/-1 mA
[15] https://www.youtube.com/watch?v=UbXDh1cYR88
      Kennlinienschreiber mit variablen Treppenstufengenerator
[16] https://www.youtube.com/watch?v=DciN_z7VIQE
      Kennlinienschreiber: Vergleich npn BC548 mit pnp BC558
[17] https://www.youtube.com/watch?v=N3BHx4Dii8s




Kalle Rüßmann
