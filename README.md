# Digital_Image_Forensic_Tools
Dokumentation von Tools, welche im Rahmen des Studiums im Modul "Forensische Analyse von Bildern und Videos" entwickelt wurden

## Error Level Analysis (ELA) Tool zur Bildforensik

### Beschreibung
Die Error Level Analysis (ELA) Methode ist eine Möglichkeit, zur Erkennung möglicher Bildmanipulationen wie "Copy Move". ELA ist ein gängiges Verfahren in der digitalen Bildforensik, das darauf abzielt, inkonsistente Kompressionsstufen in einem Bild zu identifizieren.

### Funktionsweise
Die ELA-Methode basiert auf der Wiederholung des JPEG-Kompressionsprozesses auf einem Bild. Bei jedem Kompressionsdurchlauf gehen bestimmte Bildinformationen verloren und es entstehen charakteristische Artefakte. Wenn ein Teil eines Bildes nachträglich bearbeitet wurde, zeigt dieser Bereich in der Regel eine andere Kompressionsgeschichte und reagiert somit anders auf weitere Kompression.

Das Skript führt folgende Schritte durch:
1. **Erkennung von Kompressionsunterschieden**: Das Originalbild wird erneut mit einer verringerten Qualität komprimiert. Anschließend wird die absolute Differenz zwischen dem Original und der komprimierten Version berechnet.

2. **Maske über das ELA-Bild legen**: Die berechnete Differenz wird als Maske auf das Originalbild überlagert, um visuell hervorzuheben, wo signifikante Unterschiede auftreten.

3. **Durchführung und Speicherung der Ergebnisse**: Das Tool führt die ELA für verschiedene Qualitätsstufen durch und speichert die resultierenden Bilder in einem definierten Ordner.

### Installation und Abhängigkeiten
Um das Skript zu verwenden, benötigen Sie:
- Python 3.x
- OpenCV-Python (`cv2`): Für Bildverarbeitungsaufgaben
- Pillow (`PIL`): Für grundlegende Bildoperationen
- tqdm: Für die Darstellung von Fortschrittsbalken

Diese Bibliotheken können über pip installiert werden:
```
pip install opencv-python pillow tqdm
```

### Verwendung
1. Klonen Sie das Repository oder laden Sie das Skript herunter.
2. Führen Sie das Skript in einer Python-Umgebung aus.
3. Geben Sie den Pfad zum zu analysierenden Bild an, wenn dazu aufgefordert wird.

Das Skript erstellt einen neuen Ordner auf dem Desktop und speichert die Analyseergebnisse dort ab.

### Limitationen und Hinweise
- ELA ist nicht fehlerfrei und kann zu falsch-positiven Ergebnissen führen, insbesondere in Bereichen mit homogenen Farben oder Texturen wie Himmel oder glatten Flächen.
- Die Effektivität von ELA kann durch die Qualität und das Format des Originalbildes beeinflusst werden.
- Ergebnisse sollten immer kritisch betrachtet und idealerweise durch andere forensische Methoden ergänzt werden.

### Quellen
- Huang, Hui-Yu & Ai-Jhen Ciou (2019) "Copy-move forgery detection for image forensics using the superpixel segmentation and the Helmert transformation"
  https://jivp-eurasipjournals.springeropen.com/articles/10.1186/s13640-019-0469-9
- Miedrich, Lätizia (2020) "Prototypische Implementierung und Evaluierung von Detektoren für digitale Bildmanipulationen"
  https://monami.hs-mittweida.de/frontdoor/deliver/index/docId/13766/file/Masterarbeit_Laetizia_Miedrich.pdf

  

---

*Dieses Dokument wurde zur Verbesserung der Lesbarkeit und Nutzerfreundlichkeit formatiert. Änderungen und Erweiterungen sind willkommen.*

