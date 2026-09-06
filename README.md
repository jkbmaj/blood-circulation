# Krwiobieg

Interaktywna, animowana w czasie rzeczywistym symulacja układu krwionośnego człowieka — sylwetka męska i żeńska, widok od przodu. Jeden plik HTML, czysty JavaScript i Canvas 2D, bez zależności.

## Podgląd

https://jkbmaj.github.io/blood-circulation/krwiobieg.html

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
- Żyły obwodowe: przepływ ciągły, modulowany pompą oddechową. Żyły centralne (główne, szyjne, wątrobowe, nerkowe) i żyły płucne: wzorzec dopplerowski S–D–A — największy napływ w czasie skurczu komór (fala S), mniejszy we wczesnym rozkurczu (fala D), zahamowanie lub krótkie cofnięcie przy skurczu przedsionka (fala A).
- Włośniczki: przepływ wolny, prawie ciągły; kolor cząstki zależy od pozycji na odcinku.
- Drobne naczynia: rekurencyjny generator gałęzi (3 odcinki z losowym skrętem, 2–3 dzieci, długość × 0.6, szerokość × 0.62) ograniczony maską `Path2D` obrysu ciała lub płata płuca przez `isPointInPath`. Gałąź wychodząca poza obrys najpierw próbuje skręcić, potem się urywa. Generator jest deterministyczny (mulberry32).
- Żyłki są generowane tym samym generatorem, a lista punktów jest odwracana, żeby przepływ szedł do serca.
- Czas wyrzutu komór (LVET ≈ 0,75·QT) skaluje się jak odstęp QT (Bazett: k·√RR, k = 0,37 dla mężczyzn i 0,40 dla kobiet), więc udział skurczu w cyklu rośnie z tętnem: ok. 0,30 przy 70/min, ok. 0,44 przy 150/min — rozkurcz skraca się nieproporcjonalnie bardziej. Skurcz przedsionków poprzedza skurcz komór o stały odstęp PR ≈ 160 ms.
- EKG: PR i QRS stałe w milisekundach, QT skraca się z tętnem.
- Objętość wyrzutowa rośnie o ok. 28 % i osiąga plateau przy tętnie ok. 115/min (40–50 % obciążenia maksymalnego u osób nietrenujących), powyżej 170/min lekko spada; pojemność minutowa = tętno × objętość wyrzutowa; czas obiegu = objętość krwi / pojemność minutowa.
- Ciśnienie skurczowe rośnie z obciążeniem; rozkurczowe pozostaje płaskie lub lekko spada (wysiłek dynamiczny).

## Zgodność z literaturą

| Parametr w symulacji | Wartość | Źródło |
|---|---|---|
| Objętość krwi: 5,5 l (M) / 4,5 l (K) | 70–75 ml/kg (M), 65–70 ml/kg (K) | [Determinants and reference values for blood volume… (Am J Hematol)](https://onlinelibrary.wiley.com/doi/full/10.1002/ajh.27162) |
| Hemoglobina: 15,0 / 13,5 g/dl | zakresy 13,5–17,5 (M), 12,0–15,5 (K) | [Medscape — Hemoglobin Concentration](https://emedicine.medscape.com/article/2085614-overview) |
| Objętość wyrzutowa: 75 / 60 ml, pojemność minutowa 5,3 / 4,2 l/min | serce kobiety: SV niższa o ok. 20–23 %, CO niższe o 16–22 % | [Sex Matters: A Comprehensive Comparison of Female and Male Hearts (Front Physiol 2022)](https://www.frontiersin.org/journals/physiology/articles/10.3389/fphys.2022.831179/full), [Differences in Cardiac Output… Between Sexes (PMC)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8970825/) |
| Plateau objętości wyrzutowej | 40–50 % obciążenia maksymalnego (nietrenujący) | [Does Stroke Volume Increase During an Incremental Exercise? Systematic Review](https://www.sciencedirect.com/org/science/article/pii/S1874192416000214), [Left ventricular mechanical limitations to stroke volume (AJP Heart 2011)](https://journals.physiology.org/doi/full/10.1152/ajpheart.00314.2011) |
| Ciśnienie rozkurczowe przy wysiłku | bez zmian lub lekki spadek | [Polish Archives of Internal Medicine](https://www.mp.pl/paim/issue/article/15114/), [Dynamic Exercise — ScienceDirect Topics](https://www.sciencedirect.com/topics/medicine-and-dentistry/dynamic-exercise) |
| Czas skurczu = k·√RR | Bazett 1920; k = 0,37 (M), 0,40 (K) | [The Duration of Systole in an Electrocardiogram (PMC)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7328879/), [LITFL — Bazett formula](https://litfl.com/bazett-formula/) |
| PR ≈ 160 ms, QT 350–450 ms | PR 120–200 ms; skurcz przedsionków ~100 ms | [MedSchool — PR interval](https://medschool.co/tests/ecg-basics/the-pr-interval), [LITFL — QT interval](https://litfl.com/qt-interval-ecg-library/) |
| Wzorzec przepływu w żyłach centralnych i płucnych: fale S, D, A | S > D, fala A = cofnięcie 1–16 % | [SVC and hepatic vein Doppler in healthy adults (JACC)](https://www.jacc.org/doi/10.1016/S0735-1097(87)80343-1), [Doppler Flow Velocity Patterns of SVC, IVC, Hepatic Vein (J Am Soc Echocardiogr)](https://www.sciencedirect.com/science/article/abs/pii/S0894731714803866) |
| Prędkość fali tętna: ok. 2 m/s w skali sylwetki | aorta zdrowego dorosłego 6–8 m/s — celowe spowolnienie ok. 3× | [Age-related values of aortic PWV (J Hum Hypertens)](https://www.nature.com/articles/s41371-020-00466-4) |
| Pień trzewny → t. krezkowa górna → t. nerkowe → t. krezkowa dolna | T12 → L1 → L1–L2 → L3 | [StatPearls — Abdominal Aorta](https://www.ncbi.nlm.nih.gov/books/NBK525964/), [StatPearls — Superior Mesenteric Artery](https://www.ncbi.nlm.nih.gov/books/NBK519560/) |
| T. kręgowa odchodzi od t. podobojczykowej | pierwsza gałąź t. podobojczykowej | [StatPearls — Subclavian Arteries](https://www.ncbi.nlm.nih.gov/books/NBK539736/), [TeachMeAnatomy — Vertebral Artery](https://teachmeanatomy.info/neck/vessels/arterial/vertebral-artery/) |
| Prawa nerka niżej niż lewa | o 2–8 cm z powodu wątroby | [StatPearls — Kidneys](https://www.ncbi.nlm.nih.gov/books/NBK482385/) |
| Rozdwojenie tchawicy na wysokości kąta mostka | T4/T5 | [Radiopaedia — Carina](https://radiopaedia.org/articles/carina), [StatPearls — Angle of Louis](https://www.ncbi.nlm.nih.gov/books/NBK459336/) |

## Zastrzeżenia

- Kolory są umowne — krew odtlenowana jest ciemnoczerwona, nie niebieska.
- Prędkości cząstek nie są w skali: w organizmie krew w aorcie płynie średnio ok. 30 cm/s, we włośniczkach ok. 0,05 cm/s (różnica ~600×); w symulacji stosunek jest rzędu 4×, inaczej włośniczki wyglądałyby na nieruchome ([Blood Flow Velocity — ScienceDirect Topics](https://www.sciencedirect.com/topics/medicine-and-dentistry/blood-flow-velocity)).
- Obie sylwetki mają wspólne tętno z suwaka; w rzeczywistości spoczynkowe tętno kobiet jest średnio o kilka uderzeń wyższe.
- Przebieg drobnych naczyń jest ilustracyjny; nazwane naczynia główne odpowiadają anatomii w uproszczeniu (np. pień trzewny i tętnice wieńcowe są rysowane schematycznie, obieg kończyn nie ma wszystkich gałęzi).
- Wartości liczbowe są typowe dla zdrowej osoby dorosłej i pochodzą z prostego modelu, nie z pomiaru.
