
## Wstęp

Jedną z funkcjonalności pakietu statystycznego R jest możliwość analizy danych geograficznych oraz zdolność do rysowania map danych. Pakiet R zawiera bogaty zestaw dodatkowych bibliotek oraz funkcji kreślenia, które można zastosować do danych przestrzennych. Zaletą jest fakt, iż ciągle powstają nowe pakiety pozwalające na tworzenie m.in. różnego rodzaju map. Lista wybranych pakietów do analizy danych przestrzennych znajduje się na stronie internetowej programu statystycznego R pod [linkiem](https://cran.r-project.org/web/views/Spatial.html) [dostęp 28.05.2018r.]

## Pakiety pozwalające na tworzenie map 

Najbardziej popularne pakiety do pracy z danymi przestrzennymi to m.in.:

+ **sp** - zapewnia sposób odczytywania i wyświetlania plików typu _shapefile_
+ **rgeos** - zajmuje się topografią przestrzenną 
+ **rgdal** - udostępnia połączenie lub dostęp do biblioteki abstrakcji danych geoprzestrzennych (GDAL - _Geospatial Data Abstraction Library_) oraz ich transformacji
+ **maptools** - zapewnia narzędzia do odczytu danych geograficznych, w szczególności plików _shapefile_
+ **maps** - pozwala na tworzenie prostych map i kartogramów dla całego świata oraz poszczególnych krajów 
+ **mapdata** - uzupełnia pakiet _maps_ o dodatkowe mapy 
+ **mapproj** - wykonuje operacje rzutowania map
+ **maptree** - ułatwia wyświetlanie analiz drzew decyzyjnych i regresyjnych danych przestrzennych 
+ **shapefiles** - odczytuje pliki _shapefile_

## Grupa plików typu _shapefile_ 

Wspomniane powyżej pliki _shapefile_ są jednym z najczęściej spotykanych formatów plików grafiki wektorowej. Format ten jest używany w Systemach Informacji Geograficznej (GIS - _Geographic Information System_). Format _shapefile_ został opracowany przez firmę ESRI (_Environmental Systems Research Institute_). W plikach o tym formacie można zapisywać obiekty wektorowe np. punkty i linie. Każdy z tych obiektów posiada tabelę z atrybutami, w której można umieścić parametry danych obiektów np. nazwę, długość, powierzchnię i współrzędne. 

Najczęściej zbiór danych, który jest zawarty w plikach typu _shapefile_ obejmuje zestaw minimum 3 pliki z następującymi rozszerzeniami: 

+ **.shp** - zawierający szczegółowe dane o współrzędnych kształtów, przechowujący geometrię obiektu np. granice Polski 
+ **.dbf** - przechowujący informacje (atrybuty) dotyczące kształtów danych obiektów w formie tabelarycznej np. nazwy województw w Polsce, statystyki dotyczące analizowanych krajów 
+ **.shx** - służący do indeksowania danych, pozwalający na szybkie przeszukiwanie danych ponieważ przyspiesza odczytywanie plików z geometrią 

Z plikiem o rozszerzeniu **.shp** muszą być połączone minimum pliki w formatach **.dbf** oraz **.shx**. Bez nich plik nie może działać samodzielnie. W tym celu konieczne jest, aby w jednym folderze roboczym zlokalizowane były wspomniane trzy obowiązkowe pliki. 

Opcjonalnie występujące formaty to: 

+ **.prj** - plik zawierający informacje o układzie współrzędnych oraz ich odwzorowania
+ **.sbn, .sbx** - zawierają indeksy przestrzenne obiektów 
+ **.atx** - tworzy indeksy dla atrybutów 
+ **.isx, .mxs** - tworzą indeksy poprawiające geokodowanie 
+ **.xml** - zawiera plik z metadanymi

## Przykłady generowania map z wykorzystaniem wybranych pakietów R 

Zanim zostanie wygenerowana mapa, należy pobrać zestaw plików _shapefile_ z ogólnodostępnego zbioru danych przestrzennych Centralnego Ośrodka Dokumentacji Geodezyjnej i Kartograficznej. Zasoby na stronie internetowej CODKiG są bezpłatne i można z nich korzystać również do celów komercyjnych. 
Bezpośredni [link](https://www.gis-support.pl/downloads/wojewodztwa.zip) do archiwum zawierającego pliki _shapefile_ dotyczące województw w Polsce. Pliki pochodzą z Państwowego Rejestru Granic, który jest urzędową bazą danych stanowiącą podstawę dla innych systemów informacji przestrzennej zawierających dane dotyczące podziałów terytorialnych Polski (m.in. przebieg granic, powierzchnię jednostek zasadniczego podziału terytorialnego tj. gmin, powiatów oraz województw) 


Kolejnym krokiem jest zainstalowanie oraz wczytanie dodatkowych bibliotek z repozytorium _CRAN_:
```{r warning=FALSE, results='hide', message=FALSE}
#install.packages("sp")
#install.packages("rgdal")
library("sp")
library("rgdal")

```


Wczytanie danych przestrzennych poprzez wykorzystanie funkcji _readOGR_, której argumentem jest plik _województwa.shp_:

```{r results='hide'}
polska=readOGR(dsn="./mapy/województwa.shp")
polska@data
names(polska)
```

W pliku znajduje się 29 atrybutów (kolumn), które opisują 16 województw. 
W celu uproszczenia pracy można ograniczyć liczbę atrybutów do nazwy województwa oraz powierzchni każdego z województw. W tym celu kolumnie _jpt nazwa_ zmieniono nazwę na _nazwa.woj_ oraz kolumnie _jpt powier_ na _powierzchnia.woj_. W wyniku tego otrzymano listę z nazwami województw oraz ich powierzchnią wyrażoną w kilometrach kwadratowych. 

```{r}
polska@data=polska@data[, c(6,16)]
names(polska@data)=c("nazwa.woj", "powierzchnia.woj")
polska@data
```

Zastosowanie slotu _data_ umożliwia stworzenie ramki danych na której można wykonywać różnego rodzaju operacje:

```{r}
sum(polska@data$powierzchnia.woj) # Ile wynosi łączna powierzchnia Polski? 
polska@data[which.min(polska@data[,2]),] # Które z województw ma najmniejeszą powierzchnię?
polska@data[which.max(polska@data[,2]),] # A które największą?
mean(polska@data$powierzchnia.woj) # Jaka jest średnia powierzchnia województw w Polsce?
```

Wizualizację mapy Polski można wykonać przy pomocy funkcji _plot_. Wykorzystując jej parametry można otrzymać różnego rodzaju przekształcenia rysunku np. dodawać tytuły, zmieniać kolor wypełniający powierzchnię lub zmieniać grubość granic. Istnieje również możliwość wyodrębnienia wybranego województwa i stworzenia dla niego oddzielnej mapy. 

```{r}
par(mfrow=c(2,2))
plot(polska, main="Polska", cex.main=1.5)
plot(polska[polska$nazwa.woj=="mazowieckie", ], col="lightgray", main="Województwo mazowieckie", lwd=2.5, cex.main=1.5)
plot(polska[polska$nazwa.woj=="opolskie", ], col="lightblue", main="Województwo opolskie", lwd=2.5, cex.main=1.5)
plot(polska, main="Województwo mazowieckie \ni opolskie\n", cex.main=1.5)
legtxt=c("woj. mazowiezkie", "woj. opolskie")
kolory=c("lightgray", "lightblue")
legend("bottomleft", legend=legtxt, fill=kolory,cex=1.75, bty="n",ncol=1,x.intersp=0.1,y.intersp=0.7)
plot(polska[polska$nazwa.woj=="mazowieckie", ], col="lightgray", lwd=2.5, add=TRUE)
plot(polska[polska$nazwa.woj=="opolskie", ], col="lightblue", lwd=2.5, add=TRUE)
```

Kolejnym sposobem na wygenerowanie map jest użycie pakietów _maps_ oraz _mapdata_. 

Instalacja oraz implementacja wymaganych bibliotek:

```{r, warning=FALSE}
#install.packages("maps")
#install.packages("mapdata")
library("maps")
library("mapdata")
```

Gdy zostanie wykonana funkcja _map()_ bez jakichkolwiek parametrów, domyślnie program zwróci mapę świata z zaznaczonymi konturami wszystkich krajów. 
Funkcja _map.axes_ dodaje do uprzednio wczytanej mapy osie z szerokością oraz długością geograficzną. Natomiast funkcja _map.scale_ nanosi skalę. 

```{r}
map() # Wygenerowanie mapy
map.axes() # Dodanie osi współrzędnych geograficznych 
map.scale() # Dodanie skali 

```

Wykorzystując możliwość podania współrzędnych geograficznych tj. szerokości (parametr _xlim_) oraz długości (parametr _ylim_), można wyodrębnić oraz pokazać dowolny fragment mapy świata. Parametr _fill_ oraz _col_ dotyczą wypełnienia kolorem powierzchni krajów. Wartość logiczna _TRUE_ oznacza zastosowanie wypełnienia, a parametr _col_ określa kolor jakim ma zostać wypełniony fragment. Parametr _cex.axis_ w funkcji pokazującej osie współrzędnych odpowiada  za wielkość czcionki określającej współrzędną geograficzną.   
Poniższa sekcja kodu generuje mapę przedstawiającą Polskę wraz z krajami sąsiednimi. Powierzchnia wszystkich krajów zamalowana została wybranym kolorem.  

```{r}
map(xlim=c(12,26), ylim=c(48,56), fill = TRUE, col="lightgray")
map.axes(cex.axis=2)
```

Niewypełniona mapa konturowa nie wygląda atrakcyjnie. Dzięki funkcji _points_ istnieje możliwość naniesienia punktów na podstawie współrzędnych geograficznych. Etykiety punktów tj. nazwy miast zostały przypisane poprzez funkcję _text_, która współgra z funkcją _points_. Dana funkcja odwołuje się do odpowiedniej kolumny we wczytanej ramce danych z nazwami miast i ich współrzędnymi. Ramka danych została wczytana na podstawie stworzonego pliku z rozszerzeniem _.csv_ zawierającego wspomniane elementy. Współrzędne wybranych miast w Polsce zostały pobrane ze [strony internetowej](https://www.wspolrzedne.pl/). 

Współrzędne geograficzne podawane przez ten serwis internetowy to współrzędne w formacie _DMS_ (stopnie, minuty, sekundy) oraz w formacie _DD_ (stopnie dziesiętne), gdzie separatorem dziesiętnym jest kropka. Na potrzeby pracy wykorzystany został format _DD_. 

Stworzony plik _.csv_ zawiera nagłówki, a separatorem kolumn jest średnik.
Ramka danych z wybranymi największymi miastami w Polsce wygląda następująco:

```{r}
miasta=read.csv("./mapy/wspolrzedne.csv", header = TRUE, sep=";")
miasta
```

Kolejna sekcja kodu wykonuje polecenie naniesienia punktów wraz z etykietami danych na podstawie współrzędnych geograficznych pobranych z ramki danych. Parametry _cex_ określają wielkość tekstu etykiet, wielkość punktów oraz wielkość cyfr naniesionych na skalę osi współrzędnych geograficznych. Parametr _pos_ określa miejsce umieszczenia etykiety w stosunku do położenia punktu (tu: wartość 3 oznacza umieszczenie tekstu nad punktem). Parametr _pch_ określa kształt punktu (zamalowana kropka), natomiast _col_ jego kolor (czerwony). Ostateczny wygląd mapy:

```{r}
map(xlim=c(12,26), ylim=c(48,56), fill = TRUE, col="lightgray", lty=5)
map.axes(cex.axis=2)
map.scale(cex=2)
points(miasta$x, miasta$y, pch=19, col="red", cex=1.5)
text(c(miasta$x, miasta$y), miasta$Miejscowość)
with(miasta[1:length(miasta),], text(miasta$y~miasta$x, labels=miasta[,1], cex=1.5, pos=3))
```


##Podsumowanie 

Możliwości przedstawienia różnego rodzaju zjawisk na wygenerowanych mapach w pakiecie statystycznym R są praktycznie nieograniczone. Niewątpliwym atutem tego programu  jest dostępność wielu bibliotek z funkcjami, które umożliwiają stworzenie różnego rodzaju map oraz analizę danych geograficznych w zależności od potrzeb. Praca miała na celu pokazanie podstawowych możliwości jakie oferuje pakiet statystyczny R w zakresie tworzenia map geograficznych. 


##Bibliografia 

+ Anderson E. C., _Making maps with R_ [online:] http://eriqande.github.io/rep-res-web/lectures/making-maps-with-R.html
+ Bivand R., _Applied Spatial Data Analysis with R_ [online:] http://geog.uoregon.edu/bartlein/courses/geog495/lec06.html
+ Lawrence R., Cheshire J., Oldroyd R., _Introuction to visualising spatial data in R_ [online:] https://cran.r-project.org/doc/contrib/intro-spatial-rl.pdf 
+ Strona internetowa pakietu statystycznego R:  https://cran.r-project.org/web/views/Spatial.html

**Wykorzystane zbiory danych**

+ Pliki _shapefile_ zostały pobrane ze strony internetowej Centralnego Ośrodka Dokumentacji Geodezyjnej i Kartograficznej:
https://www.gis-support.pl/downloads/wojewodztwa.zip

+ Współrzędne wybranych miast w Polsce zostały pozyskane ze strony internetowej: https://www.wspolrzedne.pl/

** Załączniki**

+ Plik _wspolrzedne.csv_ zawierający współrzędne geograficzne wybranych miast w Polsce 
+	Plik _Mapy w R.rmd_ zawierający całą pracę wraz z użytymi kodami w R. Raport został stworzony przy użyciu _R Markdown_.  



