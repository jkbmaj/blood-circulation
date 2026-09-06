# Krwiobieg

Interaktywna, animowana w czasie rzeczywistym symulacja układu krwionośnego człowieka — sylwetka męska i żeńska, widok od przodu. Jeden plik HTML, czysty JavaScript i Canvas 2D, bez zależności.

## Podgląd

- Wersja hostowana: https://claude.ai/code/artifact/1031b1c6-f528-4984-a42a-75e9294bf0ec
- GitHub Pages (po włączeniu Pages dla gałęzi `main`): https://jkbmaj.github.io/blood-circulation/krwiobieg.html

## Uruchomienie

Otwórz `krwiobieg.html` w przeglądarce. Nie jest potrzebny serwer ani build. Fonty są ładowane z Google Fonts; bez sieci strona działa na fontach zastępczych.

## Co pokazuje

- **Obieg duży** — aorta, tętnice szyjne, kręgowe, skroniowe, twarzowe, ramienne, promieniowe, łokciowe, międzyżebrowe, wątrobowa, śledzionowa, nerkowe, krezkowa, biodrowe, udowe, piszczelowe; żyły szyjne, ramienne, odpromieniowe, odłokciowe, pośrodkowa łokcia, udowe, odpiszczelowe, odstrzałkowe, nerkowe, wątrobowa, żyła wrotna, żyła główna dolna.
- **Obieg mały** — pień płucny, tętnice płucne biegnące równolegle do drzewa oskrzelowego, żyły płucne uchodzące do lewego przedsionka.
- **Płuca** — prawe trójpłatowe, lewe dwupłatowe z wcięciem sercowym, szczeliny międzypłatowe, tchawica z pierścieniami, oskrzela główne, płatowe i segmentowe, przepona. Płuca rozszerzają się przy wdechu.
- **Serce** — skurcz komór i przedsionków, bruzda wieńcowa, tętnice wieńcowe, żyła wielka serca.
- **Drobne naczynia** — proceduralnie generowane drzewa tętniczek i żyłek w obrębie kończyn, głowy, narządów jamy brzusznej i płuc. Liście drzew to włośniczki: cząstki zmieniają kolor czerwony → niebieski na obwodzie i niebieski → czerwony w płucach.
- **EKG** (odprowadzenie II) zsynchronizowane z fazą cyklu serca.
- **Parametry hemodynamiczne** dla każdej sylwetki: objętość wyrzutowa, pojemność minutowa, czas pełnego obiegu, szacunkowe ciśnienie, hemoglobina, objętość krwi.

## Sterowanie

| Element | Działanie |
|---|---|
| Mężczyzna / Kobieta / Obie | wybór sylwetki |
| suwak tętna, presety Sen / Spoczynek / Marsz / Wysiłek | 40–180 uderzeń/min |
| Zatrzymaj / Wznów, spacja | pauza |
| etykiety | nazwy naczyń i narządów |
| drobne naczynia | drzewa tętniczek i żyłek |
| narządy | płuca, wątroba, żołądek, śledziona, nerki, jelita |

## Model

- Każde naczynie jest polilinią zapisaną **w kierunku przepływu**. Cząstka krwi ma jeden stopień swobody — odległość `d` wzdłuż ścieżki.
- Tętnice: prędkość zależy od fazy skurczu komór opóźnionej o `d / PWV` (fala tętna rozchodzi się od serca; prędkość fali spowolniona ok. 3× względem fizjologii, żeby była widoczna). Ściana tętnicy pulsuje z tym samym opóźnieniem.
- Żyły: przepływ ciągły, modulowany pompą oddechową i skurczem przedsionka.
- Włośniczki: przepływ wolny, prawie ciągły; kolor cząstki zależy od pozycji na odcinku.
- Drobne naczynia: rekurencyjny generator gałęzi (3 odcinki z losowym skrętem, 2–3 dzieci, długość × 0.6, szerokość × 0.62) ograniczony maską `Path2D` obrysu ciała lub płata płuca przez `isPointInPath`. Gałąź wychodząca poza obrys najpierw próbuje skręcić, potem się urywa. Generator jest deterministyczny (mulberry32).
- Żyłki są generowane tym samym generatorem, a lista punktów jest odwracana, żeby przepływ szedł do serca.
- Objętość wyrzutowa rośnie z tętnem do ok. 150/min, potem spada; pojemność minutowa = tętno × objętość wyrzutowa; czas obiegu = objętość krwi / pojemność minutowa.

## Zastrzeżenia

Kolory są umowne — krew odtlenowana jest ciemnoczerwona, nie niebieska. Przebieg drobnych naczyń jest ilustracyjny; nazwane naczynia główne odpowiadają anatomii w uproszczeniu. Wartości liczbowe są typowe dla zdrowej osoby dorosłej i pochodzą z prostego modelu, nie z pomiaru.
