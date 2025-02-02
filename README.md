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
tb_colors <- toupper(c(
  
  "#F6A323", # Laranja 
  "#1d1d1b", # Cinza mais escuro
  "#6f7171", # Cinza mais claro
  "#ececec", # Cinza quase-branco 
  "#41ACBD"  # Azul
  
  ))
```

``` r
library(ggplot2)
library(tibble)
library(dplyr)

rgb <- col2rgb(tb_colors)
tsp <- TSP::as.TSP(dist(t(rgb)))
sol <- TSP::solve_TSP(tsp, control = list(repetitions = 1e3))
lab <- convertColor(t(rgb), 'sRGB', 'Lab')
tb_colors <- tb_colors[order(lab[, 'L'])]

tibble(x = 1:length(tb_colors), y = 1, fill = tb_colors) %>%
  ggplot(aes(x, y, fill = fill)) +
  geom_col(show.legend = F, width = 1) +
  theme(
    axis.title = element_blank(),
    axis.text.y = element_blank(),
    axis.text.x = element_text(vjust = 5),
    axis.ticks = element_blank(),
    panel.background = element_blank()
  ) +
  scale_fill_manual(values = tb_colors, breaks = tb_colors) +
  scale_x_continuous(breaks = 1:length(tb_colors), labels = tb_colors)
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
#>  [1] "#1D1D1B" "#414241" "#656767" "#5F848A" "#4B9EAC" "#69AA9A" "#B9A656"
#>  [8] "#F4AB39" "#F0CB92" "#ECECEC"
```

``` r

seq_to_blue   <- toupper(c("#ececec", "#41ACBD"))
seq_to_orange <- toupper(c("#ececec", "#F6A323"))

par(mfrow=c(4,1))

barplot(rep(1, 200),
        yaxt = "n",
        space = c(0, 0),
        border = NA, 
        col= colorRampPalette(tb_colors)(200),
        main = "Diverging: full")

barplot(rep(1, 50),
        yaxt = "n",
        space = c(0, 0),
        border = NA, 
        col= colorRampPalette(seq_to_blue)(50),
        main = "Diverging: Sequential to blue")

barplot(rep(1, 50),
        yaxt = "n",
        space = c(0, 0),
        border = NA, 
        col= colorRampPalette(seq_to_orange)(50),
        main = "Diverging: Sequential to orange")

barplot(rep(1, length(tb_colors)),
        yaxt = "n",
        space = c(0, 0),
        border = NA, 
        col= tb_colors,
        main = "Qualitative")
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
pal <- colorRampPalette(tb_colors[3:5])(numero_de_cores)

ggplot(mtcars, aes(x = disp, y = mpg, fill = as.factor(cyl))) + 
  geom_point(shape = 21, size = 4, alpha = .6) +
  # aplica theme_tb e paleta de cores
  theme_tb() +
  scale_fill_manual(values = pal)
```

<img src="README_files/figure-gfm/unnamed-chunk-6-1.png" style="display: block; margin: auto;" />

``` r
numero_de_cores <- dplyr::n_distinct(diamonds$cut)
pal <- colorRampPalette(tb_colors)(numero_de_cores)

ggplot(midwest, aes(x = area, y = poptotal, fill = state)) +
  geom_point(alpha = .4, size = 2, shape = 21) +
  # aplica theme_tb e paleta de cores
  theme_tb() +
  xlim(c(0, 0.1)) + 
  ylim(c(0, 500000)) + 
  scale_fill_manual(values = pal)
```

<img src="README_files/figure-gfm/unnamed-chunk-7-1.png" style="display: block; margin: auto;" />

``` r

numero_de_cores <- dplyr::n_distinct(diamonds$clarity)
pal <- colorRampPalette(tb_colors)(numero_de_cores)

ggplot(diamonds, aes(x = log(price), fill = clarity)) + 
  geom_histogram(color = "black") +
  theme_tb() +
  scale_fill_manual(values = pal)
```

<img src="README_files/figure-gfm/unnamed-chunk-8-1.png" style="display: block; margin: auto;" />

``` r
test <- list(tibble(y = 1:10, x = LETTERS[1:10]))

rep(test, 5) %>% 
  purrr::set_names(sprintf("Grupo %s", 1:5)) %>% 
  tibble::enframe(name = "grp") %>% 
  tidyr::unnest(value) %>% 
  ggplot(aes(x, y, fill = grp)) +
  geom_col(show.legend = F, color = "black") +
  facet_wrap(~ grp, ncol = 2) + 
  scale_fill_manual(values = tb_colors) +
  theme_tb()
```

<img src="README_files/figure-gfm/unnamed-chunk-9-1.png" style="display: block; margin: auto;" />
