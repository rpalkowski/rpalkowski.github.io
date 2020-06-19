---
title: "Mapy w R"
author: "Rados�aw Pa�kowski"
date: "28 maja 2018"
output: 
  word_document: 
    fig_height: 12
    fig_width: 14.5
---

## Wst�p

Jedn� z funkcjonalno�ci pakietu statystycznego R jest mo�liwo�� analizy danych geograficznych oraz zdolno�� do rysowania map danych. Pakiet R zawiera bogaty zestaw dodatkowych bibliotek oraz funkcji kre�lenia, kt�re mo�na zastosowa� do danych przestrzennych. Zalet� jest fakt, i� ci�gle powstaj� nowe pakiety pozwalaj�ce na tworzenie m.in. r�nego rodzaju map. Lista wybranych pakiet�w do analizy danych przestrzennych znajduje si� na stronie internetowej programu statystycznego R pod [linkiem](https://cran.r-project.org/web/views/Spatial.html) [dost�p 28.05.2018r.]

## Pakiety pozwalaj�ce na tworzenie map 

Najbardziej popularne pakiety do pracy z danymi przestrzennymi to m.in.:

+ **sp** - zapewnia spos�b odczytywania i wy�wietlania plik�w typu _shapefile_
+ **rgeos** - zajmuje si� topografi� przestrzenn� 
+ **rgdal** - udost�pnia po��czenie lub dost�p do biblioteki abstrakcji danych geoprzestrzennych (GDAL - _Geospatial Data Abstraction Library_) oraz ich transformacji
+ **maptools** - zapewnia narz�dzia do odczytu danych geograficznych, w szczeg�lno�ci plik�w _shapefile_
+ **maps** - pozwala na tworzenie prostych map i kartogram�w dla ca�ego �wiata oraz poszczeg�lnych kraj�w 
+ **mapdata** - uzupe�nia pakiet _maps_ o dodatkowe mapy 
+ **mapproj** - wykonuje operacje rzutowania map
+ **maptree** - u�atwia wy�wietlanie analiz drzew decyzyjnych i regresyjnych danych przestrzennych 
+ **shapefiles** - odczytuje pliki _shapefile_

## Grupa plik�w typu _shapefile_ 

Wspomniane powy�ej pliki _shapefile_ s� jednym z najcz�ciej spotykanych format�w plik�w grafiki wektorowej. Format ten jest u�ywany w Systemach Informacji Geograficznej (GIS - _Geographic Information System_). Format _shapefile_ zosta� opracowany przez firm� ESRI (_Environmental Systems Research Institute_). W plikach o tym formacie mo�na zapisywa� obiekty wektorowe np. punkty i linie. Ka�dy z tych obiekt�w posiada tabel� z atrybutami, w kt�rej mo�na umie�ci� parametry danych obiekt�w np. nazw�, d�ugo��, powierzchni� i wsp�rz�dne. 

Najcz�ciej zbi�r danych, kt�ry jest zawarty w plikach typu _shapefile_ obejmuje zestaw minimum 3 pliki z nast�puj�cymi rozszerzeniami: 

+ **.shp** - zawieraj�cy szczeg�owe dane o wsp�rz�dnych kszta�t�w, przechowuj�cy geometri� obiektu np. granice Polski 
+ **.dbf** - przechowuj�cy informacje (atrybuty) dotycz�ce kszta�t�w danych obiekt�w w formie tabelarycznej np. nazwy wojew�dztw w Polsce, statystyki dotycz�ce analizowanych kraj�w 
+ **.shx** - s�u��cy do indeksowania danych, pozwalaj�cy na szybkie przeszukiwanie danych poniewa� przyspiesza odczytywanie plik�w z geometri� 

Z plikiem o rozszerzeniu **.shp** musz� by� po��czone minimum pliki w formatach **.dbf** oraz **.shx**. Bez nich plik nie mo�e dzia�a� samodzielnie. W tym celu konieczne jest, aby w jednym folderze roboczym zlokalizowane by�y wspomniane trzy obowi�zkowe pliki. 

Opcjonalnie wyst�puj�ce formaty to: 

+ **.prj** - plik zawieraj�cy informacje o uk�adzie wsp�rz�dnych oraz ich odwzorowania
+ **.sbn, .sbx** - zawieraj� indeksy przestrzenne obiekt�w 
+ **.atx** - tworzy indeksy dla atrybut�w 
+ **.isx, .mxs** - tworz� indeksy poprawiaj�ce geokodowanie 
+ **.xml** - zawiera plik z metadanymi

## Przyk�ady generowania map z wykorzystaniem wybranych pakiet�w R 

Zanim zostanie wygenerowana mapa, nale�y pobra� zestaw plik�w _shapefile_ z og�lnodost�pnego zbioru danych przestrzennych Centralnego O�rodka Dokumentacji Geodezyjnej i Kartograficznej. Zasoby na stronie internetowej CODKiG s� bezp�atne i mo�na z nich korzysta� r�wnie� do cel�w komercyjnych. 
Bezpo�redni [link](https://www.gis-support.pl/downloads/wojewodztwa.zip) do archiwum zawieraj�cego pliki _shapefile_ dotycz�ce wojew�dztw w Polsce. Pliki pochodz� z Pa�stwowego Rejestru Granic, kt�ry jest urz�dow� baz� danych stanowi�c� podstaw� dla innych system�w informacji przestrzennej zawieraj�cych dane dotycz�ce podzia��w terytorialnych Polski (m.in. przebieg granic, powierzchni� jednostek zasadniczego podzia�u terytorialnego tj. gmin, powiat�w oraz wojew�dztw) 


Kolejnym krokiem jest zainstalowanie oraz wczytanie dodatkowych bibliotek z repozytorium _CRAN_:
```{r warning=FALSE, results='hide', message=FALSE}
#install.packages("sp")
#install.packages("rgdal")
library("sp")
library("rgdal")

```


Wczytanie danych przestrzennych poprzez wykorzystanie funkcji _readOGR_, kt�rej argumentem jest plik _wojew�dztwa.shp_:

```{r results='hide'}
polska=readOGR(dsn="./mapy/wojew�dztwa.shp")
polska@data
names(polska)
```

W pliku znajduje si� 29 atrybut�w (kolumn), kt�re opisuj� 16 wojew�dztw. 
W celu uproszczenia pracy mo�na ograniczy� liczb� atrybut�w do nazwy wojew�dztwa oraz powierzchni ka�dego z wojew�dztw. W tym celu kolumnie _jpt nazwa_ zmieniono nazw� na _nazwa.woj_ oraz kolumnie _jpt powier_ na _powierzchnia.woj_. W wyniku tego otrzymano list� z nazwami wojew�dztw oraz ich powierzchni� wyra�on� w kilometrach kwadratowych. 

```{r}
polska@data=polska@data[, c(6,16)]
names(polska@data)=c("nazwa.woj", "powierzchnia.woj")
polska@data
```

Zastosowanie slotu _data_ umo�liwia stworzenie ramki danych na kt�rej mo�na wykonywa� r�nego rodzaju operacje:

```{r}
sum(polska@data$powierzchnia.woj) # Ile wynosi ��czna powierzchnia Polski? 
polska@data[which.min(polska@data[,2]),] # Kt�re z wojew�dztw ma najmniejesz� powierzchni�?
polska@data[which.max(polska@data[,2]),] # A kt�re najwi�ksz�?
mean(polska@data$powierzchnia.woj) # Jaka jest �rednia powierzchnia wojew�dztw w Polsce?
```

Wizualizacj� mapy Polski mo�na wykona� przy pomocy funkcji _plot_. Wykorzystuj�c jej parametry mo�na otrzyma� r�nego rodzaju przekszta�cenia rysunku np. dodawa� tytu�y, zmienia� kolor wype�niaj�cy powierzchni� lub zmienia� grubo�� granic. Istnieje r�wnie� mo�liwo�� wyodr�bnienia wybranego wojew�dztwa i stworzenia dla niego oddzielnej mapy. 

```{r}
par(mfrow=c(2,2))
plot(polska, main="Polska", cex.main=1.5)
plot(polska[polska$nazwa.woj=="mazowieckie", ], col="lightgray", main="Wojew�dztwo mazowieckie", lwd=2.5, cex.main=1.5)
plot(polska[polska$nazwa.woj=="opolskie", ], col="lightblue", main="Wojew�dztwo opolskie", lwd=2.5, cex.main=1.5)
plot(polska, main="Wojew�dztwo mazowieckie \ni opolskie\n", cex.main=1.5)
legtxt=c("woj. mazowiezkie", "woj. opolskie")
kolory=c("lightgray", "lightblue")
legend("bottomleft", legend=legtxt, fill=kolory,cex=1.75, bty="n",ncol=1,x.intersp=0.1,y.intersp=0.7)
plot(polska[polska$nazwa.woj=="mazowieckie", ], col="lightgray", lwd=2.5, add=TRUE)
plot(polska[polska$nazwa.woj=="opolskie", ], col="lightblue", lwd=2.5, add=TRUE)
```

Kolejnym sposobem na wygenerowanie map jest u�ycie pakiet�w _maps_ oraz _mapdata_. 

Instalacja oraz implementacja wymaganych bibliotek:

```{r, warning=FALSE}
#install.packages("maps")
#install.packages("mapdata")
library("maps")
library("mapdata")
```

Gdy zostanie wykonana funkcja _map()_ bez jakichkolwiek parametr�w, domy�lnie program zwr�ci map� �wiata z zaznaczonymi konturami wszystkich kraj�w. 
Funkcja _map.axes_ dodaje do uprzednio wczytanej mapy osie z szeroko�ci� oraz d�ugo�ci� geograficzn�. Natomiast funkcja _map.scale_ nanosi skal�. 

```{r}
map() # Wygenerowanie mapy
map.axes() # Dodanie osi wsp�rz�dnych geograficznych 
map.scale() # Dodanie skali 

```

Wykorzystuj�c mo�liwo�� podania wsp�rz�dnych geograficznych tj. szeroko�ci (parametr _xlim_) oraz d�ugo�ci (parametr _ylim_), mo�na wyodr�bni� oraz pokaza� dowolny fragment mapy �wiata. Parametr _fill_ oraz _col_ dotycz� wype�nienia kolorem powierzchni kraj�w. Warto�� logiczna _TRUE_ oznacza zastosowanie wype�nienia, a parametr _col_ okre�la kolor jakim ma zosta� wype�niony fragment. Parametr _cex.axis_ w funkcji pokazuj�cej osie wsp�rz�dnych odpowiada  za wielko�� czcionki okre�laj�cej wsp�rz�dn� geograficzn�.   
Poni�sza sekcja kodu generuje map� przedstawiaj�c� Polsk� wraz z krajami s�siednimi. Powierzchnia wszystkich kraj�w zamalowana zosta�a wybranym kolorem.  

```{r}
map(xlim=c(12,26), ylim=c(48,56), fill = TRUE, col="lightgray")
map.axes(cex.axis=2)
```

Niewype�niona mapa konturowa nie wygl�da atrakcyjnie. Dzi�ki funkcji _points_ istnieje mo�liwo�� naniesienia punkt�w na podstawie wsp�rz�dnych geograficznych. Etykiety punkt�w tj. nazwy miast zosta�y przypisane poprzez funkcj� _text_, kt�ra wsp�gra z funkcj� _points_. Dana funkcja odwo�uje si� do odpowiedniej kolumny we wczytanej ramce danych z nazwami miast i ich wsp�rz�dnymi. Ramka danych zosta�a wczytana na podstawie stworzonego pliku z rozszerzeniem _.csv_ zawieraj�cego wspomniane elementy. Wsp�rz�dne wybranych miast w Polsce zosta�y pobrane ze [strony internetowej](https://www.wspolrzedne.pl/). 

Wsp�rz�dne geograficzne podawane przez ten serwis internetowy to wsp�rz�dne w formacie _DMS_ (stopnie, minuty, sekundy) oraz w formacie _DD_ (stopnie dziesi�tne), gdzie separatorem dziesi�tnym jest kropka. Na potrzeby pracy wykorzystany zosta� format _DD_. 

Stworzony plik _.csv_ zawiera nag��wki, a separatorem kolumn jest �rednik.
Ramka danych z wybranymi najwi�kszymi miastami w Polsce wygl�da nast�puj�co:

```{r}
miasta=read.csv("./mapy/wspolrzedne.csv", header = TRUE, sep=";")
miasta
```

Kolejna sekcja kodu wykonuje polecenie naniesienia punkt�w wraz z etykietami danych na podstawie wsp�rz�dnych geograficznych pobranych z ramki danych. Parametry _cex_ okre�laj� wielko�� tekstu etykiet, wielko�� punkt�w oraz wielko�� cyfr naniesionych na skal� osi wsp�rz�dnych geograficznych. Parametr _pos_ okre�la miejsce umieszczenia etykiety w stosunku do po�o�enia punktu (tu: warto�� 3 oznacza umieszczenie tekstu nad punktem). Parametr _pch_ okre�la kszta�t punktu (zamalowana kropka), natomiast _col_ jego kolor (czerwony). Ostateczny wygl�d mapy:

```{r}
map(xlim=c(12,26), ylim=c(48,56), fill = TRUE, col="lightgray", lty=5)
map.axes(cex.axis=2)
map.scale(cex=2)
points(miasta$x, miasta$y, pch=19, col="red", cex=1.5)
text(c(miasta$x, miasta$y), miasta$Miejscowo��)
with(miasta[1:length(miasta),], text(miasta$y~miasta$x, labels=miasta[,1], cex=1.5, pos=3))
```


##Podsumowanie 

Mo�liwo�ci przedstawienia r�nego rodzaju zjawisk na wygenerowanych mapach w pakiecie statystycznym R s� praktycznie nieograniczone. Niew�tpliwym atutem tego programu  jest dost�pno�� wielu bibliotek z funkcjami, kt�re umo�liwiaj� stworzenie r�nego rodzaju map oraz analiz� danych geograficznych w zale�no�ci od potrzeb. Praca mia�a na celu pokazanie podstawowych mo�liwo�ci jakie oferuje pakiet statystyczny R w zakresie tworzenia map geograficznych. 


##Bibliografia 

+ Anderson E. C., _Making maps with R_ [online:] http://eriqande.github.io/rep-res-web/lectures/making-maps-with-R.html
+ Bivand R., _Applied Spatial Data Analysis with R_ [online:] http://geog.uoregon.edu/bartlein/courses/geog495/lec06.html
+ Lawrence R., Cheshire J., Oldroyd R., _Introuction to visualising spatial data in R_ [online:] https://cran.r-project.org/doc/contrib/intro-spatial-rl.pdf 
+ Strona internetowa pakietu statystycznego R:  https://cran.r-project.org/web/views/Spatial.html

**Wykorzystane zbiory danych**

+ Pliki _shapefile_ zosta�y pobrane ze strony internetowej Centralnego O�rodka Dokumentacji Geodezyjnej i Kartograficznej:
https://www.gis-support.pl/downloads/wojewodztwa.zip

+ Wsp�rz�dne wybranych miast w Polsce zosta�y pozyskane ze strony internetowej: https://www.wspolrzedne.pl/

** Za��czniki**

+ Plik _wspolrzedne.csv_ zawieraj�cy wsp�rz�dne geograficzne wybranych miast w Polsce 
+	Plik _Mapy w R.rmd_ zawieraj�cy ca�� prac� wraz z u�ytymi kodami w R. Raport zosta� stworzony przy u�yciu _R Markdown_.  



