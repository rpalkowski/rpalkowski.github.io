[Powrót](./)

[Mapy w większej rozdzielczości](https://github.com/rpalkowski/wybory-2020/tree/master/I_tura)


------------------------------------------------------------------------

<img src="./plots/frekwencja_mapa-1.png" style="display: block; margin: auto;" />

<img src="./plots/glosy_niewazne-1.png" style="display: block; margin: auto;" />

<img src="./plots/zwyciezcy_gminy-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_top3-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_last5-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_rt-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_ad-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_sh-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_kb-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_wkk-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_rb-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_sz-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_mj-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_pt-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_ww-1.png" style="display: block; margin: auto;" />

<img src="./plots/poparcie_proc_mp-1.png" style="display: block; margin: auto;" />

------------------------------------------------------------------------

### Dodatek - Czy w obwodowych komisjach więcej jest kobiet czy mężczyzn?

Jak wygląda zbiór danych?

    ## # A tibble: 6 x 7
    ##   `TERYT gminy` `Nazwa gminy`  Powiat   `Nr obw.` Nazwisko  Imiona   Funkcja    
    ##   <chr>         <chr>          <chr>        <dbl> <chr>     <chr>    <chr>      
    ## 1 020101        m. Bolesławiec bolesła~         1 Glinicka~ Marta A~ Przewodnic~
    ## 2 020101        m. Bolesławiec bolesła~         1 Kowalczyk Alicja ~ Zastępca P~
    ## 3 020101        m. Bolesławiec bolesła~         1 Kunecka   Aleksan~ Członek    
    ## 4 020101        m. Bolesławiec bolesła~         1 Mikołaje~ Marzena~ Członek    
    ## 5 020101        m. Bolesławiec bolesła~         1 Lisiecki  Dominik~ Członek    
    ## 6 020101        m. Bolesławiec bolesła~         1 Czerepak  Daria    Członek

#### Rozpoznawanie płci po imieniu

**Założenie:** *Polskie imiona żeńskie kończą się na literę “a” z niektórymi
wyjątkami imion męskich tj. Bonawentura, Barnaba, Kosma, Dyzma, Juda.
Pozostałe imiona to imiona męskie.*

Płeć członków komisji osądzimy na podstawie pierwszego imienia danej
osoby.

<img src="./plots/plec_przewazajaca-1.png" style="display: block; margin: auto;" />

------------------------------------------------------------------------

[Powrót](./)


