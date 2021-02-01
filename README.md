Identidade visual com `ggplot2`
================

  - [Paleta de cores](#paleta-de-cores)
  - [Definindo sequencias](#definindo-sequencias)
  - [Template para `ggplot2`](#template-para-ggplot2)

<p align="center">

<img src="logo_200x200.png" />

</p>

Um pequeno guia para aplicação da identidade visual da Transparência
Brasil em relatórios e documentos diversos que envolvem análise de
dados.

## Paleta de cores

``` r
tb_colors <- c(
  
  "#F6A323", # Azul 
  "gray95",   # em branco?
  "#41ACBD"  # Laranja
  
  
  )

# TODO: incluir mais cores, talvez uma cor quente para o vermelho?
```

``` r
scales::show_col(tb_colors)
```

<img src="README_files/figure-gfm/unnamed-chunk-2-1.png" style="display: block; margin: auto;" />

# Definindo sequencias

A função `colorRampParlette(...)(n)` interpola um conjunto qualquer de
cores definindo uma sequência. O parâmetro externo `n` é o número de
interpolações que atravessam o conjunto de cores. A função retorna as
cores em um vetor de caracteres com o código hexadecimal:

``` r
# número de cores que eu quero
n <- 10
colorRampPalette(tb_colors)(n) # display n cores
#>  [1] "#F6A323" "#F5B451" "#F4C67F" "#F3D7AD" "#F2E9DB" "#DEEAEC" "#B7DAE0"
#>  [8] "#8FCBD4" "#68BBC8" "#41ACBD"
```

``` r
par(mfrow=c(3,1))

barplot(rep(1, 50),
        yaxt = "n",
        space = c(0, 0),
        border = NA, 
        col= colorRampPalette(tb_colors)(50),
        main = "Diverging")

barplot(rep(1, 50),
        yaxt = "n",
        space = c(0, 0),
        border = NA, 
        col= colorRampPalette(tb_colors[2:3])(50),
        main = "Sequential to blue")

barplot(rep(1, 50),
        yaxt = "n",
        space = c(0, 0),
        border = NA, 
        col= colorRampPalette(tb_colors[2:1])(50),
        main = "Sequential to yellow")
```

<img src="README_files/figure-gfm/unnamed-chunk-4-1.png" style="display: block; margin: auto;" />

# Template para `ggplot2`

``` r

theme_tb <- function (base_size = 12, base_family = "Helvetica",
                      legend.position="bottom", legend_size= 10) {
  
  theme_grey(base_size = base_size, base_family = base_family) %+replace% 
  theme(
    
    legend.position = legend.position , 
    legend.margin=unit(-.2,"cm"),
    legend.text=element_text(size=legend_size),
    legend.title = element_blank(), # size=.5
    
    axis.text = element_text(size = rel(0.8)),
    axis.ticks = element_line(colour = "black"), 
    
    legend.key = element_rect(colour = "grey80"), 
    legend.key.size = unit(.3, "cm"),
    
    panel.background = element_rect(fill = "white", colour = NA),
    panel.border = element_rect(fill = NA,  colour = "grey50"), 
    panel.grid.major = element_line(colour = "white", size = 0.2),
    panel.grid.minor = element_line(colour = "white",  size = 0.5), 
    
    strip.background = element_rect(fill = "grey80", colour = "grey50", size = 0.2)
    
  )
  
}
```

Aplicando o template em um exemplo:

``` r
library(ggplot2)

numero_de_cores <- dplyr::n_distinct(as.factor(mtcars$cyl))
pal <- colorRampPalette(tb_colors)(numero_de_cores)

ggplot(mtcars, aes(x = disp, y = mpg, fill = as.factor(cyl))) + 
  geom_point(shape = 21, size = 4, alpha = .6) +
  # aplica theme_tb e paleta de cores
  theme_tb() +
  scale_fill_manual(values = pal)
```

<img src="README_files/figure-gfm/unnamed-chunk-6-1.png" style="display: block; margin: auto;" />

> **TO DO**: incluir mais cores?

``` r
numero_de_cores <- dplyr::n_distinct(diamonds$cut)
pal <- colorRampPalette(tb_colors)(numero_de_cores)

ggplot(diamonds, aes(x = carat, y = price, color = cut)) +
  geom_point() +
  # aplica theme_tb e paleta de cores
  theme_tb() +
  scale_color_manual(values = pal)
```

<img src="README_files/figure-gfm/unnamed-chunk-7-1.png" style="display: block; margin: auto;" />

``` r

numero_de_cores <- dplyr::n_distinct(diamonds$clarity)
pal <- colorRampPalette(tb_colors)(numero_de_cores)


ggplot(diamonds, aes(x = price, fill = clarity)) + 
  geom_histogram() +
  theme_tb() +
  scale_fill_manual(values = pal)
```

<img src="README_files/figure-gfm/unnamed-chunk-8-1.png" style="display: block; margin: auto;" />
