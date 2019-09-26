ggplot\_1
================
Matthew Parker
9/26/2019

## Create weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"),
                      var = c("PRCP", "TMIN", "TMAX"), 
                      date_min = "2017-01-01",
                      date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          /Users/matthewparker/Library/Caches/rnoaa/ghcnd/USW00094728.dly

    ## file last updated:  2019-05-30 10:03:41

    ## file min/max dates: 1869-01-01 / 2019-05-31

    ## file path:          /Users/matthewparker/Library/Caches/rnoaa/ghcnd/USC00519397.dly

    ## file last updated:  2019-05-30 10:03:56

    ## file min/max dates: 1965-01-01 / 2019-05-31

    ## file path:          /Users/matthewparker/Library/Caches/rnoaa/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-05-30 10:04:33

    ## file min/max dates: 1999-09-01 / 2019-05-31

``` r
weather_df
```

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # … with 1,085 more rows

## Create a ggplot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_1_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

Alternate way of making this plot

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

saving initial plots. mostly not used by Jeff.

``` r
scatterplot = weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point()

scatterplot
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

adding color

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = 0.4)
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

why do ‘aes’ positions matter?

first

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_point(aes(color = name), alpha = 0.4) + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

vs

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = 0.4) + 
  geom_smooth(se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

facet\!

``` r
weather_df %>%
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = 0.4) + 
  geom_smooth(se = FALSE) + 
  facet_grid(~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](ggplot_1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

this is fine, but not interesting.

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp), alpha = 0.5) + 
  geom_smooth(size = 2, se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](ggplot_1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## Some extra stuff

``` r
weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_smooth(size = 2, se = FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

![](ggplot_1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

2d density

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_hex()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_binhex).

![](ggplot_1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax)) + 
  geom_bin2d()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_bin2d).

![](ggplot_1_files/figure-gfm/unnamed-chunk-11-2.png)<!-- -->

## More kinds of plots

``` r
weather_df %>% 
  ggplot(aes(x = tmax, fill = name)) + 
  geom_histogram() + 
  facet_grid(~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 3 rows containing non-finite values (stat_bin).

![](ggplot_1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

density plots

``` r
weather_df %>% 
  ggplot(aes(x = tmax, fill = name)) + 
  geom_density(alpha = 0.3)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](ggplot_1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

boxplot

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmax)) + 
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](ggplot_1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

violin plot

``` r
weather_df %>% 
  ggplot(aes(x = name, y = tmax)) + 
  geom_violin()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](ggplot_1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

ridge plot

``` r
weather_df %>% 
  ggplot(aes(x = tmax, y = name)) + 
  ggridges::geom_density_ridges()
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](ggplot_1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->
