---
title: "Mapy w R"
author: "Rados³aw Pa³kowski"
date: "28 maja 2018"
output: 
  word_document: 
    fig_height: 12
    fig_width: 14.5
---

## Wstêp

Jedn¹ z funkcjonalnoœci pakietu statystycznego R jest mo¿liwoœæ analizy danych geograficznych oraz zdolnoœæ do rysowania map danych. Pakiet R zawiera bogaty zestaw dodatkowych bibliotek oraz funkcji kreœlenia, które mo¿na zastosowaæ do danych przestrzennych. Zalet¹ jest fakt, i¿ ci¹gle powstaj¹ nowe pakiety pozwalaj¹ce na tworzenie m.in. ró¿nego rodzaju map. Lista wybranych pakietów do analizy danych przestrzennych znajduje siê na stronie internetowej programu statystycznego R pod [linkiem](https://cran.r-project.org/web/views/Spatial.html) [dostêp 28.05.2018r.]

## Pakiety pozwalaj¹ce na tworzenie map 

Najbardziej popularne pakiety do pracy z danymi przestrzennymi to m.in.:

+ **sp** - zapewnia sposób odczytywania i wyœwietlania plików typu _shapefile_
+ **rgeos** - zajmuje siê topografi¹ przestrzenn¹ 
+ **rgdal** - udostêpnia po³¹czenie lub dostêp do biblioteki abstrakcji danych geoprzestrzennych (GDAL - _Geospatial Data Abstraction Library_) oraz ich transformacji
+ **maptools** - zapewnia narzêdzia do odczytu danych geograficznych, w szczególnoœci plików _shapefile_
+ **maps** - pozwala na tworzenie prostych map i kartogramów dla ca³ego œwiata oraz poszczególnych krajów 
+ **mapdata** - uzupe³nia pakiet _maps_ o dodatkowe mapy 
+ **mapproj** - wykonuje operacje rzutowania map
+ **maptree** - u³atwia wyœwietlanie analiz drzew decyzyjnych i regresyjnych danych przestrzennych 
+ **shapefiles** - odczytuje pliki _shapefile_

## Grupa plików typu _shapefile_ 

Wspomniane powy¿ej pliki _shapefile_ s¹ jednym z najczêœciej spotykanych formatów plików grafiki wektorowej. Format ten jest u¿ywany w Systemach Informacji Geograficznej (GIS - _Geographic Information System_). Format _shapefile_ zosta³ opracowany przez firmê ESRI (_Environmental Systems Research Institute_). W plikach o tym formacie mo¿na zapisywaæ obiekty wektorowe np. punkty i linie. Ka¿dy z tych obiektów posiada tabelê z atrybutami, w której mo¿na umieœciæ parametry danych obiektów np. nazwê, d³ugoœæ, powierzchniê i wspó³rzêdne. 

Najczêœciej zbiór danych, który jest zawarty w plikach typu _shapefile_ obejmuje zestaw minimum 3 pliki z nastêpuj¹cymi rozszerzeniami: 

+ **.shp** - zawieraj¹cy szczegó³owe dane o wspó³rzêdnych kszta³tów, przechowuj¹cy geometriê obiektu np. granice Polski 
+ **.dbf** - przechowuj¹cy informacje (atrybuty) dotycz¹ce kszta³tów danych obiektów w formie tabelarycznej np. nazwy województw w Polsce, statystyki dotycz¹ce analizowanych krajów 
+ **.shx** - s³u¿¹cy do indeksowania danych, pozwalaj¹cy na szybkie przeszukiwanie danych poniewa¿ przyspiesza odczytywanie plików z geometri¹ 

Z plikiem o rozszerzeniu **.shp** musz¹ byæ po³¹czone minimum pliki w formatach **.dbf** oraz **.shx**. Bez nich plik nie mo¿e dzia³aæ samodzielnie. W tym celu konieczne jest, aby w jednym folderze roboczym zlokalizowane by³y wspomniane trzy obowi¹zkowe pliki. 

Opcjonalnie wystêpuj¹ce formaty to: 

+ **.prj** - plik zawieraj¹cy informacje o uk³adzie wspó³rzêdnych oraz ich odwzorowania
+ **.sbn, .sbx** - zawieraj¹ indeksy przestrzenne obiektów 
+ **.atx** - tworzy indeksy dla atrybutów 
+ **.isx, .mxs** - tworz¹ indeksy poprawiaj¹ce geokodowanie 
+ **.xml** - zawiera plik z metadanymi

## Przyk³ady generowania map z wykorzystaniem wybranych pakietów R 

Zanim zostanie wygenerowana mapa, nale¿y pobraæ zestaw plików _shapefile_ z ogólnodostêpnego zbioru danych przestrzennych Centralnego Oœrodka Dokumentacji Geodezyjnej i Kartograficznej. Zasoby na stronie internetowej CODKiG s¹ bezp³atne i mo¿na z nich korzystaæ równie¿ do celów komercyjnych. 
Bezpoœredni [link](https://www.gis-support.pl/downloads/wojewodztwa.zip) do archiwum zawieraj¹cego pliki _shapefile_ dotycz¹ce województw w Polsce. Pliki pochodz¹ z Pañstwowego Rejestru Granic, który jest urzêdow¹ baz¹ danych stanowi¹c¹ podstawê dla innych systemów informacji przestrzennej zawieraj¹cych dane dotycz¹ce podzia³ów terytorialnych Polski (m.in. przebieg granic, powierzchniê jednostek zasadniczego podzia³u terytorialnego tj. gmin, powiatów oraz województw) 


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

W pliku znajduje siê 29 atrybutów (kolumn), które opisuj¹ 16 województw. 
W celu uproszczenia pracy mo¿na ograniczyæ liczbê atrybutów do nazwy województwa oraz powierzchni ka¿dego z województw. W tym celu kolumnie _jpt nazwa_ zmieniono nazwê na _nazwa.woj_ oraz kolumnie _jpt powier_ na _powierzchnia.woj_. W wyniku tego otrzymano listê z nazwami województw oraz ich powierzchni¹ wyra¿on¹ w kilometrach kwadratowych. 

```{r}
polska@data=polska@data[, c(6,16)]
names(polska@data)=c("nazwa.woj", "powierzchnia.woj")
polska@data
```

Zastosowanie slotu _data_ umo¿liwia stworzenie ramki danych na której mo¿na wykonywaæ ró¿nego rodzaju operacje:

```{r}
sum(polska@data$powierzchnia.woj) # Ile wynosi ³¹czna powierzchnia Polski? 
polska@data[which.min(polska@data[,2]),] # Które z województw ma najmniejesz¹ powierzchniê?
polska@data[which.max(polska@data[,2]),] # A które najwiêksz¹?
mean(polska@data$powierzchnia.woj) # Jaka jest œrednia powierzchnia województw w Polsce?
```

Wizualizacjê mapy Polski mo¿na wykonaæ przy pomocy funkcji _plot_. Wykorzystuj¹c jej parametry mo¿na otrzymaæ ró¿nego rodzaju przekszta³cenia rysunku np. dodawaæ tytu³y, zmieniaæ kolor wype³niaj¹cy powierzchniê lub zmieniaæ gruboœæ granic. Istnieje równie¿ mo¿liwoœæ wyodrêbnienia wybranego województwa i stworzenia dla niego oddzielnej mapy. 

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

Kolejnym sposobem na wygenerowanie map jest u¿ycie pakietów _maps_ oraz _mapdata_. 

Instalacja oraz implementacja wymaganych bibliotek:

```{r, warning=FALSE}
#install.packages("maps")
#install.packages("mapdata")
library("maps")
library("mapdata")
```

Gdy zostanie wykonana funkcja _map()_ bez jakichkolwiek parametrów, domyœlnie program zwróci mapê œwiata z zaznaczonymi konturami wszystkich krajów. 
Funkcja _map.axes_ dodaje do uprzednio wczytanej mapy osie z szerokoœci¹ oraz d³ugoœci¹ geograficzn¹. Natomiast funkcja _map.scale_ nanosi skalê. 

```{r}
map() # Wygenerowanie mapy
map.axes() # Dodanie osi wspó³rzêdnych geograficznych 
map.scale() # Dodanie skali 

```

Wykorzystuj¹c mo¿liwoœæ podania wspó³rzêdnych geograficznych tj. szerokoœci (parametr _xlim_) oraz d³ugoœci (parametr _ylim_), mo¿na wyodrêbniæ oraz pokazaæ dowolny fragment mapy œwiata. Parametr _fill_ oraz _col_ dotycz¹ wype³nienia kolorem powierzchni krajów. Wartoœæ logiczna _TRUE_ oznacza zastosowanie wype³nienia, a parametr _col_ okreœla kolor jakim ma zostaæ wype³niony fragment. Parametr _cex.axis_ w funkcji pokazuj¹cej osie wspó³rzêdnych odpowiada  za wielkoœæ czcionki okreœlaj¹cej wspó³rzêdn¹ geograficzn¹.   
Poni¿sza sekcja kodu generuje mapê przedstawiaj¹c¹ Polskê wraz z krajami s¹siednimi. Powierzchnia wszystkich krajów zamalowana zosta³a wybranym kolorem.  

```{r}
map(xlim=c(12,26), ylim=c(48,56), fill = TRUE, col="lightgray")
map.axes(cex.axis=2)
```

Niewype³niona mapa konturowa nie wygl¹da atrakcyjnie. Dziêki funkcji _points_ istnieje mo¿liwoœæ naniesienia punktów na podstawie wspó³rzêdnych geograficznych. Etykiety punktów tj. nazwy miast zosta³y przypisane poprzez funkcjê _text_, która wspó³gra z funkcj¹ _points_. Dana funkcja odwo³uje siê do odpowiedniej kolumny we wczytanej ramce danych z nazwami miast i ich wspó³rzêdnymi. Ramka danych zosta³a wczytana na podstawie stworzonego pliku z rozszerzeniem _.csv_ zawieraj¹cego wspomniane elementy. Wspó³rzêdne wybranych miast w Polsce zosta³y pobrane ze [strony internetowej](https://www.wspolrzedne.pl/). 

Wspó³rzêdne geograficzne podawane przez ten serwis internetowy to wspó³rzêdne w formacie _DMS_ (stopnie, minuty, sekundy) oraz w formacie _DD_ (stopnie dziesiêtne), gdzie separatorem dziesiêtnym jest kropka. Na potrzeby pracy wykorzystany zosta³ format _DD_. 

Stworzony plik _.csv_ zawiera nag³ówki, a separatorem kolumn jest œrednik.
Ramka danych z wybranymi najwiêkszymi miastami w Polsce wygl¹da nastêpuj¹co:

```{r}
miasta=read.csv("./mapy/wspolrzedne.csv", header = TRUE, sep=";")
miasta
```

Kolejna sekcja kodu wykonuje polecenie naniesienia punktów wraz z etykietami danych na podstawie wspó³rzêdnych geograficznych pobranych z ramki danych. Parametry _cex_ okreœlaj¹ wielkoœæ tekstu etykiet, wielkoœæ punktów oraz wielkoœæ cyfr naniesionych na skalê osi wspó³rzêdnych geograficznych. Parametr _pos_ okreœla miejsce umieszczenia etykiety w stosunku do po³o¿enia punktu (tu: wartoœæ 3 oznacza umieszczenie tekstu nad punktem). Parametr _pch_ okreœla kszta³t punktu (zamalowana kropka), natomiast _col_ jego kolor (czerwony). Ostateczny wygl¹d mapy:

```{r}
map(xlim=c(12,26), ylim=c(48,56), fill = TRUE, col="lightgray", lty=5)
map.axes(cex.axis=2)
map.scale(cex=2)
points(miasta$x, miasta$y, pch=19, col="red", cex=1.5)
text(c(miasta$x, miasta$y), miasta$Miejscowoœæ)
with(miasta[1:length(miasta),], text(miasta$y~miasta$x, labels=miasta[,1], cex=1.5, pos=3))
```


##Podsumowanie 

Mo¿liwoœci przedstawienia ró¿nego rodzaju zjawisk na wygenerowanych mapach w pakiecie statystycznym R s¹ praktycznie nieograniczone. Niew¹tpliwym atutem tego programu  jest dostêpnoœæ wielu bibliotek z funkcjami, które umo¿liwiaj¹ stworzenie ró¿nego rodzaju map oraz analizê danych geograficznych w zale¿noœci od potrzeb. Praca mia³a na celu pokazanie podstawowych mo¿liwoœci jakie oferuje pakiet statystyczny R w zakresie tworzenia map geograficznych. 


##Bibliografia 

+ Anderson E. C., _Making maps with R_ [online:] http://eriqande.github.io/rep-res-web/lectures/making-maps-with-R.html
+ Bivand R., _Applied Spatial Data Analysis with R_ [online:] http://geog.uoregon.edu/bartlein/courses/geog495/lec06.html
+ Lawrence R., Cheshire J., Oldroyd R., _Introuction to visualising spatial data in R_ [online:] https://cran.r-project.org/doc/contrib/intro-spatial-rl.pdf 
+ Strona internetowa pakietu statystycznego R:  https://cran.r-project.org/web/views/Spatial.html

**Wykorzystane zbiory danych**

+ Pliki _shapefile_ zosta³y pobrane ze strony internetowej Centralnego Oœrodka Dokumentacji Geodezyjnej i Kartograficznej:
https://www.gis-support.pl/downloads/wojewodztwa.zip

+ Wspó³rzêdne wybranych miast w Polsce zosta³y pozyskane ze strony internetowej: https://www.wspolrzedne.pl/

** Za³¹czniki**

+ Plik _wspolrzedne.csv_ zawieraj¹cy wspó³rzêdne geograficzne wybranych miast w Polsce 
+	Plik _Mapy w R.rmd_ zawieraj¹cy ca³¹ pracê wraz z u¿ytymi kodami w R. Raport zosta³ stworzony przy u¿yciu _R Markdown_.  



