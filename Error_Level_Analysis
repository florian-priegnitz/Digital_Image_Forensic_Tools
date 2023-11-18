# Importieren der benötigten Bibliotheken
import cv2
import os
import datetime
from PIL import Image, ImageChops
from tqdm import tqdm

# Funktion, die Kompressionsunterschiede in einem Bild erkennt. Sie vergleicht das Originalbild mit einer komprimierten
# Version und erstellt eine Maske, die die Unterschiede hervorhebt.
def erkennen_kompressionsunterschiede(original_bild_pfad, qualitaet=90, schwellwertfaktor=2):
    # Originalbild einlesen
    # cv2.imread(filePath): Liest ein Bild von der angegebenen Dateipfad und gibt es als NumPy-Array zurück.
    # Es wird häufig verwendet, um Bilder in ein Skript zu laden.
    original_bild = cv2.imread(original_bild_pfad)

    # Pfad für das temporäre, komprimierte Bild festlegen
    komprimiertes_bild_pfad = 'temp.jpg'

    # Das Originalbild mit verringerter Qualität speichern, um Kompression zu simulieren
    # cv2.imwrite(filePath, image, [cv2.IMWRITE_JPEG_QUALITY, quality]): Speichert ein Bild unter dem angegebenen
    # Dateipfad. Der Parameter cv2.IMWRITE_JPEG_QUALITY ermöglicht es, die Qualität des JPEG-Bildes anzupassen,
    # wobei quality ein Wert zwischen 0 (niedrigste Qualität, höchste Kompression) und 100 (höchste Qualität,
    # niedrigste Kompression) ist.
    cv2.imwrite(komprimiertes_bild_pfad, original_bild, [int(cv2.IMWRITE_JPEG_QUALITY), qualitaet])

    # Das komprimierte Bild einlesen
    komprimiertes_bild = cv2.imread(komprimiertes_bild_pfad)

    # Differenz zwischen dem Original- und komprimierten Bild berechnen
    # cv2.absdiff(image1, image2): Berechnet die absolute Differenz zwischen zwei Bildern. Dies wird verwendet,
    # um Unterschiede zwischen dem Originalbild und seiner komprimierten Version zu finden.
    diff_bild = cv2.absdiff(original_bild, komprimiertes_bild)

    # Das Differenzbild in Graustufen konvertieren
    # cv2.cvtColor(image, cv2.COLOR_BGR2GRAY): Konvertiert ein Bild von BGR (Blau, Grün, Rot) Farbraum zu Graustufen.
    diff_grau = cv2.cvtColor(diff_bild, cv2.COLOR_BGR2GRAY)

    # Mittelwert und Standardabweichung der Grauwerte berechnen
    # cv2.meanStdDev(image): Berechnet den Mittelwert und die Standardabweichung der Pixelwerte im Bild.
    # Nützlich für statistische Analysen von Bildern.
    mittelwert, standardabweichung = cv2.meanStdDev(diff_grau)

    # Schwellenwert für die Maske berechnen
    schwellenwert = mittelwert[0][0] + schwellwertfaktor * standardabweichung[0][0]

    # Schwellenwert auf das Graustufenbild anwenden, um eine Maske zu erstellen
    # cv2.threshold(image, thresholdValue, maxValue, cv2.THRESH_BINARY): Wendet einen Schwellenwert auf ein Bild an.
    # Pixelwerte, die über dem Schwellenwert liegen, werden auf maxValue gesetzt, während alle anderen auf 0
    # gesetzt werden.
    _, diff_maske = cv2.threshold(diff_grau, schwellenwert, 255, cv2.THRESH_BINARY)

    # Maske zurückgeben
    return diff_maske

# Funktion, die eine Maske auf das Error Level Analysis (ELA)-Bild überlagert.
# Diese Maske hebt Bereiche hervor, in denen signifikante Unterschiede zwischen dem Originalbild und seiner
# komprimierten Version bestehen.
def maske_auf_ela_bild_ueberlagern(ela_bild_pfad, maske, skala=0.5):
    # ELA-Bild einlesen
    ela_bild = cv2.imread(ela_bild_pfad)

    # Die Maske von Graustufen in BGR umwandeln und rot färben
    gefaerbte_maske = cv2.cvtColor(maske, cv2.COLOR_GRAY2BGR)
    gefaerbte_maske[maske == 255] = [0, 0, 255]

    # Maske mit dem ELA-Bild überlagern
    # cv2.addWeighted(source1, alpha, source2, beta, gamma): Kombiniert zwei Bilder mit spezifischen Gewichtungen 
    # (alpha und beta) und einem zusätzlichen Wert gamma.
    ela_mit_maske = cv2.addWeighted(ela_bild, 1, gefaerbte_maske, skala, 0)

    # Pfad für das gespeicherte Bild festlegen und Bild speichern
    ausgabe_pfad = ela_bild_pfad.replace('x_keine_maske_', 'mit_maske_')
    cv2.imwrite(ausgabe_pfad, ela_mit_maske)

    # Gespeicherten Pfad zurückgeben
    return ausgabe_pfad

# Hauptfunktion für die Durchführung der Error Level Analysis (ELA) mit einer Maske.
# Diese Funktion erstellt das ELA-Bild, indem sie das Originalbild mit einer komprimierten Version vergleicht,
# und überlagert dann die Maske auf dieses ELA-Bild.
def error_level_analyse_mit_maske(original_bild_pfad, qualitaet, ausgabe_ordner, schwellwertfaktor, skala):
    # Maske für Kompressionsunterschiede berechnen
    maske = erkennen_kompressionsunterschiede(original_bild_pfad, qualitaet, schwellwertfaktor)

    # Originalbild und temporäres Bild einlesen
    original = Image.open(original_bild_pfad)
    temp_pfad = os.path.join(ausgabe_ordner, 'temp.jpg')

    # Dieser Befehl speichert das original Bild als JPEG-Datei am temp_pfad. Der Parameter qualitaet=qualitaet gibt an,
    # mit welcher Qualität das Bild gespeichert werden soll. Diese Funktion wird verwendet, um eine komprimierte
    # Version des Originalbildes zu erstellen, die dann für die ELA-Analyse verwendet wird. Durch die Veränderung
    # der Qualität und das erneute Einlesen des Bildes werden potenzielle Kompressionsartefakte eingeführt, die dann
    # analysiert werden können.
    original.save(temp_pfad, 'JPEG', qualitaet=qualitaet)
    temporaer = Image.open(temp_pfad)

    # Differenz zwischen Original und temporärem Bild berechnen (ELA-Bild)
    # Der Befehl ela_bild = ImageChops.difference(original, temporaer) verwendet die ImageChops.difference()-Funktion
    # aus der PIL-Bibliothek (Python Imaging Library), um die Differenz zwischen zwei Bildern zu berechnen. In diesem
    # speziellen Kontext werden das Originalbild (original) und seine temporär gespeicherte, komprimierte Version
    # (temporaer) verwendet. Hier ist, was dabei geschieht:
    # Berechnung der Differenz: Die Funktion ImageChops.difference() berechnet die Differenz zwischen den
    # entsprechenden Pixeln der beiden Bilder. Für jeden Pixel im Bild werden die Farbwerte (in jedem Kanal)
    # des Originalbildes von den Farbwerten des komprimierten Bildes subtrahiert.
    ela_bild = ImageChops.difference(original, temporaer)

    # Namen und Pfad für das ELA-Bild festlegen
    zeitstempel = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
    ela_bild_name = f"x_keine_maske_{zeitstempel}_ela_{qualitaet}_{schwellwertfaktor}_{skala}.jpg"
    ela_bild_pfad = os.path.join(ausgabe_ordner, ela_bild_name)

    # ELA-Bild speichern und Fortschrittsbalken anzeigen
    with tqdm(total=1, desc="Speichern des ELA-Bildes") as pbar:
        ela_bild.save(ela_bild_pfad, 'JPEG')
        pbar.update(1)

    # Temporäres Bild löschen
    os.remove(temp_pfad)

    # Maske auf das ELA-Bild überlagern und Pfad zurückgeben
    return maske_auf_ela_bild_ueberlagern(ela_bild_pfad, maske, skala)

# Hauptfunktion des Skripts, die den Benutzer nach einem Bildpfad fragt und die Error Level Analysis mit Maskierung
# für verschiedene Qualitätsstufen durchführt.
# Die Ergebnisse werden in einem spezifizierten Ordner gespeichert.
def main():
    # Benutzer nach dem Pfad des Bildes fragen
    original_bild_pfad = input("Bitte geben Sie den Pfad zu Ihrem Bild ein: ")

    # Pfad zum Desktop-Ordner und zum Bild-Forensik-Ordner festlegen
    desktop_pfad = os.path.expanduser("~/Desktop")
    bild_forensik_ordner = os.path.join(desktop_pfad, "Bild_Forensik")

    # Ordner für die aktuelle Sitzung basierend auf dem Zeitstempel erstellen
    zeitstempel_ordner = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
    ausgabe_ordner = os.path.join(bild_forensik_ordner, zeitstempel_ordner)
    if not os.path.exists(ausgabe_ordner):
        os.makedirs(ausgabe_ordner)

    # Listen für verschiedene Qualitätsstufen, Schwellenwerte und Skalen definieren. Da nicht jede manuelle Einstellung
    # zielführend ist und häufig mehrere Versuche unternommen werden müssen, um die richtige Werte zu finden, iteriert
    # das Skript 10 durch die Funktion mit vordefinierten Werten, welche in den Arrays hinterlegt sind. Die Werte
    # werden in den Bildnamen geschrieben, um die Einstellungen besser nachzuvollziehen.
    # Die Arrays qualitaeten, schwellenwerte und skalen:
    #
    # qualitaeten: Dieses Array enthält verschiedene Qualitätsstufen für die JPEG-Kompression. Eine niedrigere Zahl
    # bedeutet eine höhere Kompression und potenziell mehr Artefakte, während eine höhere Zahl eine niedrigere
    # Kompression und weniger Artefakte bedeutet. Das Array ermöglicht das Testen der ELA (Error Level Analysis)
    # bei verschiedenen Qualitätsstufen.
    #
    # schwellenwerte: Diese Werte werden verwendet, um die Sensitivität der Schwellenwertbildung zu steuern.
    # Ein höherer Schwellenwert bedeutet, dass nur stärkere Unterschiede als signifikant erkannt werden, während
    # ein niedrigerer Schwellenwert auch kleinere Unterschiede hervorhebt.
    #
    # skalen: Diese Werte steuern die Intensität, mit der die Maske auf das ELA-Bild überlagert wird. Ein höherer
    # Wert macht die überlagerte Maske prominenter, während ein niedrigerer Wert sie subtiler macht.
    
    qualitaeten = [90, 80, 70, 60, 50, 40, 30, 20, 10, 95]
    schwellenwerte = [1, 1.5, 2, 2.5, 3, 3.5, 4, 4.5, 5, 1.2]
    skalen = [0.5, 0.6, 0.7, 0.8, 0.9, 1.0, 1.1, 1.2, 1.3, 0.4]

    # Fortschrittsbalken für den gesamten Verarbeitungsprozess
    with tqdm(total=len(qualitaeten), desc="Verarbeitung") as pbar:
        for qualitaet, schwellenwert, skala in zip(qualitaeten, schwellenwerte, skalen):
            ela_mit_maske_pfad = error_level_analyse_mit_maske(original_bild_pfad, qualitaet, ausgabe_ordner, schwellenwert, skala)
            print(f"ELA-Bild mit Maske gespeichert in: {ela_mit_maske_pfad}")
            pbar.update(1)

    # Speicherort der Ergebnisse anzeigen
    print(f"Alle Ergebnisse wurden im Ordner {ausgabe_ordner} gespeichert.")

# Skript starten, wenn es direkt aufgerufen wird
if __name__ == "__main__":
    main()
