Русские литературные гении
================
Бердыева София в соавторстве с коллективом группы 20.Б02-с

## Задание 1

**Возьмем данные из трендов и узнаем, как меняется число запросов по
теме со временем.**

**Найти тренд отдельно по поэзии и прозе.**

**Это следует сделать через временные ряды.**

**Проблемы:**

**1. Насколько репрезентативна выборка авторов?**

**2. Как следует обобщать результаты?**

**3. Данные по малопопулярным авторам подвержены волатильности. Следует
ли всем авторам приписывать одинаковый вес или надо действовать
пропорционально интересу публики?**

**4. Прийти к выводу и, возможно, предложить объяснение. Сравнить поэзию
и прозу.**

Еще раз опишем задачу и её решение с начала до конца: необходимо оценить
популярность запросов в поисковой системе Google, относящихся к двум
группам представителей русской литературы - поэтам и прозаикам.

Как известно, среди различных эпох в русской литературе две занимают
центральное место - Золотой век (некоторые литературоведы относят к нему
чуть ли не весь 19 век) и Серебряный (начало 20 века). Если Серебряный
век относится преимущественно к поэзии, то Золотой включал в себя как
великих поэтов, так и прозаиков, поэтому для того, чтобы в нашей выборке
наличествовали одновременно представители и поэзии, и прозы, и при этом
из обоих периодов, разумно взять поэтов века Серебряного и прозаиков
Золотого.

В случае поэтов мы вновь обращаемся к принципу максимальной вариации и
подбираем представителей абсолютно различных литературнй течений (или
творящих вне каких-то определённых методов): М. Цветаева, С. Есенин и В.
Маяковский

С прозой немного сложней провести демаркацию, поэтому просто возьмем
авторов, чьи труды имеют мировую известность, и при этом в большинстве
своём являются прозаическими: Л. Толстой, Ф. Достоевский и А. Чехов

Учитывая, что наша выборка состоит всего из шести авторов, говорить о
репрезентативности довольно бессмысленно. Безусловно, можно подобрать
еще более вариативную комбинацию для достижения иллюзорной
репрезентативности, но в целях обучения инструментарию анализа временных
рядов мы сочтем наш набор приемлемым.

Отметим, что при анализе нужно учитывать сезонность популярности
авторов, возникающую из-за особенностей школьной учебной прораммы по
литературе, которая порождает всплеск в определенные периоды.

Для того, чтобы операционализировать конструкты “популярность поэзии” и
“популярность прозы”, сделаем составной компонент для каждого из
понятий, посчитав средние значения по трем авторам в месяц. Таким
образом, мы ориентируемся исключительно по интересам публики, что для
нас логичней как для социологов, вдобавок не имеющих столь глубоких
экспертных познаний в литературоведении, чтобы браться за присваивание
весов. Однако если бы мы обладали такими знаниями, можно было бы
прибегнуть к методу парных сравнений. Но где-то убыло, а где-то -
прибыло.

Также на всякий случай приведем описание выгружаемых данных,
предоставляемое Гуглом: Числа обозначают уровень интереса к теме по
отношению к наиболее высокому показателю в таблице для определенного
региона и периода времени. 100 баллов означают наивысший уровень
популярности запроса, 50 – уровень популярности запроса, вдвое меньший
по сравнению с первым случаем. 0 баллов означает местоположение, по
которому недостаточно данных о рассматриваемом запросе.

Данные, выгружаемые с помощью пакета gtrends, отличаются от тех, что мы
получаем при выгрузке вручную (вторые адекватней). Поэтому ниже в чанке
приведем код, который мог бы использоваться нами для получения данных,
но не будет, так как ручная выгрузка безопасней.

``` r
library(gtrendsR)

poets_res <- gtrends(c("Марина Ивановна Цветаева", "Владимир Маяковский", "Сергей Есенин"), geo = "RU",time = "all")
poet <- poets_res$interest_over_time

proza_res <-gtrends(keyword = c("Лев Толстой", "Фёдор Достоевский", "Антон Чехов"),  geo = "RU",time = "all") 
proza <- proza_res$interest_over_time
```

А вот “настоящий” код для подгрузки и создания всего, что нужно.
Сохраненные данные сложили на гитхабе.

``` r
poet <- read.csv("https://raw.githubusercontent.com/sophiyaberdiyeva/Russian-literature-GTrends/main/poets_data_RU.csv", skip = 2)
proza <- read.csv("https://raw.githubusercontent.com/sophiyaberdiyeva/Russian-literature-GTrends/main/proza_data_RU.csv", skip = 2)

colnames(poet) = c('time', 'tsvetayeva','mayak','esenin')
colnames(proza)=c('time','tolstoy','dostoyevskiy','chekhov')

#Считаем столбцы со средними значениями по всем авторам
poet$means <- rowMeans(poet[,c(2:4)])
proza$means <- rowMeans(proza[,c(2:4)])

head(poet)
```

    ##      time tsvetayeva mayak esenin    means
    ## 1 2004-01         25    36     16 25.66667
    ## 2 2004-02         17    44      0 20.33333
    ## 3 2004-03          0    38     14 17.33333
    ## 4 2004-04         18     0     13 10.33333
    ## 5 2004-05         16    37     23 25.33333
    ## 6 2004-06         27     0     23 16.66667

``` r
head(proza)
```

    ##      time tolstoy dostoyevskiy chekhov    means
    ## 1 2004-01      54            0      73 42.33333
    ## 2 2004-02       0           18      27 15.00000
    ## 3 2004-03      39           51       0 30.00000
    ## 4 2004-04      49           63      83 65.00000
    ## 5 2004-05      75           51      87 71.00000
    ## 6 2004-06      34           49      28 37.00000

Также создадим временные ряды для каждого из авторов и для агрег-х
средних, и посмотрим на графики

### Поэзия

``` r
ts_tsvetayeva <- ts(poet$tsvetayeva,frequency=12, start = c(2004,1),end=c(2023,5))
ts_mayak <- ts(poet$mayak,frequency=12, start = c(2004,1),end=c(2023,5))
ts_esenin <- ts(poet$esenin,frequency=12, start = c(2004,1),end=c(2023,5))
ts_poets_mean <- ts(poet$means,frequency=12, start = c(2004,1),end=c(2023,5))

#Цветаева
plot(ts_tsvetayeva)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
boxplot(ts_tsvetayeva~cycle(ts_tsvetayeva))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->

``` r
dec_tsvetayeva <- decompose(ts_tsvetayeva, type="additive")
fit_tsvetayeva <- stl(ts_tsvetayeva, s.window = "periodic")
plot(fit_tsvetayeva)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-3.png)<!-- -->

``` r
#Маяковский
plot(ts_mayak)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-4.png)<!-- -->

``` r
boxplot(ts_mayak~cycle(ts_mayak))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-5.png)<!-- -->

``` r
dec_mayak <- decompose(ts_mayak, type="additive")
fit_mayak <- stl(ts_mayak, s.window = "periodic")
plot(fit_mayak)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-6.png)<!-- -->

``` r
#Есенин
plot(ts_esenin)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-7.png)<!-- -->

``` r
boxplot(ts_esenin~cycle(ts_esenin))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-8.png)<!-- -->

``` r
dec_esenin <- decompose(ts_esenin, type="additive")
fit_esenin <- stl(ts_esenin, s.window = "periodic")
plot(fit_esenin)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-9.png)<!-- -->

``` r
#Среднее по поэтам
plot(ts_poets_mean)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-10.png)<!-- -->

``` r
boxplot(ts_poets_mean~cycle(ts_poets_mean))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-11.png)<!-- -->

``` r
dec_poets_mean <- decompose(ts_poets_mean, type="additive")
fit_poets_mean <- stl(ts_poets_mean, s.window = "periodic")
plot(fit_poets_mean)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-3-12.png)<!-- -->

### Проза

``` r
ts_tolstoy <- ts(proza$tolstoy,frequency=12, start = c(2004,1),end=c(2023,4))
ts_dostoyevskiy <- ts(proza$dostoyevskiy,frequency=12, start = c(2004,1),end=c(2023,4))
ts_chekhov <- ts(proza$chekhov,frequency=12, start = c(2004,1),end=c(2023,4))
ts_proza_mean <- ts(proza$means,frequency=12, start = c(2004,1),end=c(2023,4))


#Толстой
plot(ts_tolstoy)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
boxplot(ts_tolstoy~cycle(ts_tolstoy))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->

``` r
dec_tolstoy <- decompose(ts_tolstoy, type="additive")
fit_tolstoy <- stl(ts_tolstoy, s.window = "periodic")
plot(fit_tolstoy)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-3.png)<!-- -->

``` r
#Достоевский
plot(ts_dostoyevskiy)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-4.png)<!-- -->

``` r
boxplot(ts_dostoyevskiy~cycle(ts_dostoyevskiy))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-5.png)<!-- -->

``` r
dec_dostoyevskiy <- decompose(ts_dostoyevskiy, type="additive")
fit_dostoyevskiy <- stl(ts_dostoyevskiy, s.window = "periodic")
plot(fit_dostoyevskiy)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-6.png)<!-- -->

``` r
#Чехов
plot(ts_chekhov)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-7.png)<!-- -->

``` r
boxplot(ts_chekhov~cycle(ts_chekhov))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-8.png)<!-- -->

``` r
dec_chekhov <- decompose(ts_chekhov, type="additive")
fit_chekhov <- stl(ts_chekhov, s.window = "periodic")
plot(fit_chekhov)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-9.png)<!-- -->

``` r
#Среднее по прозаикам
plot(ts_proza_mean)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-10.png)<!-- -->

``` r
boxplot(ts_proza_mean~cycle(ts_proza_mean))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-11.png)<!-- -->

``` r
dec_proza_mean <- decompose(ts_proza_mean, type="additive")
fit_proza_mean <- stl(ts_proza_mean, s.window = "periodic")
plot(fit_proza_mean)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-4-12.png)<!-- -->

Как видно, во всех случаях интерес наиболее низок в летние месяцы и в
сентябре.

В 2007-2008 годы наблюдались пики популярности запросов по сравнению с
остальными. Также небольшой рост интереса происходил в 2015-2016 годах.
Отметим, что 2007 был объявлен годом русского языка, и в этот период
проводился комплекс мероприятий в рамках соотв. программы, а 2015 год
был годом русской литературы. Также упомянем, что для большинства
авторов - как для поэтов, так и для прозаиков, последние пять лет
показатели внимания к ним сильно снизились по сравнению с предыдущим
десятилетием. Этот тренд не коснулся только Толстого и Есенина.

## Задание 2

**Как и в прошлый раз: сделать что-то оригинальное с временными рядами,
при условии выполнения задания 1.**

В дальнейшем все вычисления будем выполнять только для усредненных по
всем авторам временных рядов

### Доп. графики в ggplot

``` r
library(ggplot2)

# Создание временного графика с помощью пакета ggplot2
# Здесь отдельные графики к прозе и поэзии
plot1<-ggplot(data = proza, aes(x = time, y = means, group = 1)) +geom_point()+
  geom_line( color =" turquoise4 ") +
  labs(x = "Year", y = "trend", 
       title = "proza_trend")

plot2<-ggplot(data = poet, aes(x = time, y = means, group = 1)) +
  geom_point() +
  geom_line( color =" turquoise4 ") +
  labs(x = "Year", y = "trend", 
       title = "poet_trend")

overlay_plot <- plot1 + geom_line(data = poet, aes(x = time, y = means, group = 1))
print(overlay_plot)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

На графике выше бирюзовым нарисована динамика среднего интереса по
прозаикам, а серым - по поэтам. В большинстве случаев уровень интереса к
прозаикам выше, чем к поэтам.

### Вангуем

``` r
library(fpp2)
# Проза, сравниваем три метода предсказания
# Различия в просмотрах по месяцам
diffPr <- diff(ts_proza_mean)
# Наивный метод
ProsaForecastNaive <- snaive(diffPr) # Функция, учитывающая сезонность тренда
summary(ProsaForecastNaive) # Residual SD: 8.5348
```

    ## 
    ## Forecast method: Seasonal naive method
    ## 
    ## Model Information:
    ## Call: snaive(y = diffPr) 
    ## 
    ## Residual sd: 8.0262 
    ## 
    ## Error measures:
    ##                      ME     RMSE      MAE  MPE MAPE MASE       ACF1
    ## Training set 0.06240487 8.026244 4.981735 -Inf  Inf    1 -0.3593677
    ## 
    ## Forecasts:
    ##          Point Forecast      Lo 80     Hi 80      Lo 95    Hi 95
    ## May 2023     -3.6666667 -13.952713  6.619379 -19.397817 12.06448
    ## Jun 2023     -3.6666667 -13.952713  6.619379 -19.397817 12.06448
    ## Jul 2023     -0.3333333 -10.619379  9.952713 -16.064483 15.39782
    ## Aug 2023      0.0000000 -10.286046 10.286046 -15.731150 15.73115
    ## Sep 2023      0.3333333  -9.952713 10.619379 -15.397817 16.06448
    ## Oct 2023      3.6666667  -6.619379 13.952713 -12.064483 19.39782
    ## Nov 2023      4.6666667  -5.619379 14.952713 -11.064483 20.39782
    ## Dec 2023     -1.3333333 -11.619379  8.952713 -17.064483 14.39782
    ## Jan 2024     12.3333333   2.047287 22.619379  -3.397817 28.06448
    ## Feb 2024     -1.3333333 -11.619379  8.952713 -17.064483 14.39782
    ## Mar 2024    -10.0000000 -20.286046  0.286046 -25.731150  5.73115
    ## Apr 2024     -3.0000000 -13.286046  7.286046 -18.731150 12.73115
    ## May 2024     -3.6666667 -18.213332 10.879999 -25.913872 18.58054
    ## Jun 2024     -3.6666667 -18.213332 10.879999 -25.913872 18.58054
    ## Jul 2024     -0.3333333 -14.879999 14.213332 -22.580539 21.91387
    ## Aug 2024      0.0000000 -14.546666 14.546666 -22.247206 22.24721
    ## Sep 2024      0.3333333 -14.213332 14.879999 -21.913872 22.58054
    ## Oct 2024      3.6666667 -10.879999 18.213332 -18.580539 25.91387
    ## Nov 2024      4.6666667  -9.879999 19.213332 -17.580539 26.91387
    ## Dec 2024     -1.3333333 -15.879999 13.213332 -23.580539 20.91387
    ## Jan 2025     12.3333333  -2.213332 26.879999  -9.913872 34.58054
    ## Feb 2025     -1.3333333 -15.879999 13.213332 -23.580539 20.91387
    ## Mar 2025    -10.0000000 -24.546666  4.546666 -32.247206 12.24721
    ## Apr 2025     -3.0000000 -17.546666 11.546666 -25.247206 19.24721

``` r
checkresiduals(ProsaForecastNaive) # p-value <  0.001
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

    ## 
    ##  Ljung-Box test
    ## 
    ## data:  Residuals from Seasonal naive method
    ## Q* = 109.64, df = 24, p-value = 6.524e-13
    ## 
    ## Model df: 0.   Total lags used: 24

``` r
# Остатки распределены нормально, белый шум отсутствует
prosa_Naive_f<-forecast(ProsaForecastNaive, h = 12)

# Метод экспоненциального сглаживания
ProsaForecastETS <- ets(ts_proza_mean) 
summary(ProsaForecastETS) # Residual SD: 5.9582
```

    ## ETS(M,N,M) 
    ## 
    ## Call:
    ##  ets(y = ts_proza_mean) 
    ## 
    ##   Smoothing parameters:
    ##     alpha = 0.0909 
    ##     gamma = 0.3297 
    ## 
    ##   Initial states:
    ##     l = 42.1935 
    ##     s = 0.7919 0.839 0.6246 1.0062 0.7434 0.6533
    ##            0.916 1.4377 1.3493 1.3468 1.3172 0.9746
    ## 
    ##   sigma:  0.1618
    ## 
    ##      AIC     AICc      BIC 
    ## 2083.653 2085.875 2135.354 
    ## 
    ## Training set error measures:
    ##                      ME     RMSE      MAE       MPE     MAPE      MASE
    ## Training set -0.6116821 6.282672 4.031688 -4.385633 12.71188 0.9844299
    ##                   ACF1
    ## Training set 0.2419458

``` r
checkresiduals(ProsaForecastETS) #  p-value <  0.001
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-6-2.png)<!-- -->

    ## 
    ##  Ljung-Box test
    ## 
    ## data:  Residuals from ETS(M,N,M)
    ## Q* = 51.051, df = 24, p-value = 0.001039
    ## 
    ## Model df: 0.   Total lags used: 24

``` r
# Остатки распределены нормально, белый шум отсутствует
prosa_ETS_f<-forecast(ProsaForecastETS, h = 12)


# Модель ARIMA 
ProsaForecastARIMA <- auto.arima(ts_proza_mean, d=1, D=1, 
                                 stepwise = FALSE, approximation = FALSE, 
                                 trace = F) 
summary(ProsaForecastARIMA) # Residual SD: 5,663
```

    ## Series: ts_proza_mean 
    ## ARIMA(1,1,2)(2,1,0)[12] 
    ## 
    ## Coefficients:
    ##           ar1     ma1      ma2     sar1     sar2
    ##       -0.7356  0.0336  -0.8472  -0.4303  -0.3634
    ## s.e.   0.0594  0.0423   0.0387   0.0837   0.0884
    ## 
    ## sigma^2 = 31.73:  log likelihood = -690.52
    ## AIC=1393.04   AICc=1393.44   BIC=1413.38
    ## 
    ## Training set error measures:
    ##                      ME     RMSE      MAE        MPE     MAPE      MASE
    ## Training set -0.0939252 5.410097 3.603215 -0.5844654 10.20951 0.8798083
    ##                    ACF1
    ## Training set 0.03787563

``` r
checkresiduals(ProsaForecastARIMA) #  p-value <  0.001 
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-6-3.png)<!-- -->

    ## 
    ##  Ljung-Box test
    ## 
    ## data:  Residuals from ARIMA(1,1,2)(2,1,0)[12]
    ## Q* = 48.342, df = 19, p-value = 0.0002289
    ## 
    ## Model df: 5.   Total lags used: 24

``` r
# Остатки распределены нормально, белый шум отсутствует
prosa_ARIMA_f<-forecast(ProsaForecastARIMA, h = 12)
summary(prosa_ARIMA_f)
```

    ## 
    ## Forecast method: ARIMA(1,1,2)(2,1,0)[12]
    ## 
    ## Model Information:
    ## Series: ts_proza_mean 
    ## ARIMA(1,1,2)(2,1,0)[12] 
    ## 
    ## Coefficients:
    ##           ar1     ma1      ma2     sar1     sar2
    ##       -0.7356  0.0336  -0.8472  -0.4303  -0.3634
    ## s.e.   0.0594  0.0423   0.0387   0.0837   0.0884
    ## 
    ## sigma^2 = 31.73:  log likelihood = -690.52
    ## AIC=1393.04   AICc=1393.44   BIC=1413.38
    ## 
    ## Error measures:
    ##                      ME     RMSE      MAE        MPE     MAPE      MASE
    ## Training set -0.0939252 5.410097 3.603215 -0.5844654 10.20951 0.8798083
    ##                    ACF1
    ## Training set 0.03787563
    ## 
    ## Forecasts:
    ##          Point Forecast    Lo 80    Hi 80    Lo 95    Hi 95
    ## May 2023       29.19369 21.97467 36.41271 18.15316 40.23423
    ## Jun 2023       26.05598 18.52320 33.58875 14.53559 37.57636
    ## Jul 2023       24.90207 17.36556 32.43857 13.37597 36.42816
    ## Aug 2023       25.12905 17.44080 32.81729 13.37089 36.88720
    ## Sep 2023       25.61518 17.92357 33.30679 13.85188 37.37848
    ## Oct 2023       29.28036 21.49903 37.06168 17.37984 41.18087
    ## Nov 2023       33.34781 25.55176 41.14385 21.42478 45.27083
    ## Dec 2023       32.28221 24.42313 40.14128 20.26279 44.30162
    ## Jan 2024       43.08393 35.20085 50.96702 31.02779 55.14007
    ## Feb 2024       44.82036 36.88682 52.75389 32.68706 56.95365
    ## Mar 2024       34.73075 26.76740 42.69410 22.55186 46.90964
    ## Apr 2024       32.18707 24.17963 40.19451 19.94075 44.43339

``` r
# Так как белый шум отсутствует везде, что не является хорошим показателем, будем ориентироваться на стандартное отклонение остатков, оно лучше всего у модели ARIMA


# Визуализация

options(scipen=10000)
autoplot(prosa_ARIMA_f) + 
  ggtitle("Предсказание по месяцам") +
  theme(plot.title = element_text(hjust = 0.5)) + xlab("")
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-6-4.png)<!-- -->

``` r
# Предсказание на следующие 12 месяцев выделено синим, синяя зона - доверительный интервал 80%,голубая - 95%

# Поэзия, сравниваем три метода предсказания
# Различия в просмотрах по месяцам
diffpo <- diff(ts_poets_mean)
# Наивный метод
PoetryForecastNaive <- snaive(diffpo) # Функция, учитывающая сезонность тренда
summary(PoetryForecastNaive) # Residual SD: 7.6604 
```

    ## 
    ## Forecast method: Seasonal naive method
    ## 
    ## Model Information:
    ## Call: snaive(y = diffpo) 
    ## 
    ## Residual sd: 5.191 
    ## 
    ## Error measures:
    ##                      ME     RMSE      MAE MPE MAPE MASE      ACF1
    ## Training set 0.02121212 5.190998 3.251515 NaN  Inf    1 -0.386471
    ## 
    ## Forecasts:
    ##          Point Forecast      Lo 80     Hi 80      Lo 95     Hi 95
    ## Jun 2023     -4.3333333 -10.985865  2.319199 -14.507503  5.840837
    ## Jul 2023     -1.6666667  -8.319199  4.985865 -11.840837  8.507503
    ## Aug 2023     -0.6666667  -7.319199  5.985865 -10.840837  9.507503
    ## Sep 2023      2.3333333  -4.319199  8.985865  -7.840837 12.507503
    ## Oct 2023      3.6666667  -2.985865 10.319199  -6.507503 13.840837
    ## Nov 2023     -1.3333333  -7.985865  5.319199 -11.507503  8.840837
    ## Dec 2023      3.0000000  -3.652532  9.652532  -7.174170 13.174170
    ## Jan 2024      1.3333333  -5.319199  7.985865  -8.840837 11.507503
    ## Feb 2024      3.0000000  -3.652532  9.652532  -7.174170 13.174170
    ## Mar 2024      2.6666667  -3.985865  9.319199  -7.507503 12.840837
    ## Apr 2024     -2.0000000  -8.652532  4.652532 -12.174170  8.174170
    ## May 2024     -4.0000000 -10.652532  2.652532 -14.174170  6.174170
    ## Jun 2024     -4.3333333 -13.741435  5.074768 -18.721782 10.055116
    ## Jul 2024     -1.6666667 -11.074768  7.741435 -16.055116 12.721782
    ## Aug 2024     -0.6666667 -10.074768  8.741435 -15.055116 13.721782
    ## Sep 2024      2.3333333  -7.074768 11.741435 -12.055116 16.721782
    ## Oct 2024      3.6666667  -5.741435 13.074768 -10.721782 18.055116
    ## Nov 2024     -1.3333333 -10.741435  8.074768 -15.721782 13.055116
    ## Dec 2024      3.0000000  -6.408101 12.408101 -11.388449 17.388449
    ## Jan 2025      1.3333333  -8.074768 10.741435 -13.055116 15.721782
    ## Feb 2025      3.0000000  -6.408101 12.408101 -11.388449 17.388449
    ## Mar 2025      2.6666667  -6.741435 12.074768 -11.721782 17.055116
    ## Apr 2025     -2.0000000 -11.408101  7.408101 -16.388449 12.388449
    ## May 2025     -4.0000000 -13.408101  5.408101 -18.388449 10.388449

``` r
checkresiduals(PoetryForecastNaive) # p-value <  0.001
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-6-5.png)<!-- -->

    ## 
    ##  Ljung-Box test
    ## 
    ## data:  Residuals from Seasonal naive method
    ## Q* = 113.19, df = 24, p-value = 0.0000000000001555
    ## 
    ## Model df: 0.   Total lags used: 24

``` r
# Остатки распределены нормально, белый шум отсутствует
poetry_Naive_f<-forecast(PoetryForecastNaive, h = 12)

# Метод экспоненциального сглаживания
PoetryForecastETS <- ets(ts_poets_mean) 
summary(PoetryForecastETS) # Residual SD: 6.2131
```

    ## ETS(A,N,A) 
    ## 
    ## Call:
    ##  ets(y = ts_poets_mean) 
    ## 
    ##   Smoothing parameters:
    ##     alpha = 0.2339 
    ##     gamma = 0.3137 
    ## 
    ##   Initial states:
    ##     l = 19.3525 
    ##     s = 16.7463 7.6882 -3.5242 -13.2352 -8.1358 -11.4039
    ##            -4.1237 0.1733 -2.3768 0.3107 9.6461 8.2351
    ## 
    ##   sigma:  4.1842
    ## 
    ##      AIC     AICc      BIC 
    ## 1952.645 1954.856 2004.410 
    ## 
    ## Training set error measures:
    ##                      ME     RMSE      MAE  MPE MAPE      MASE      ACF1
    ## Training set -0.0812561 4.056528 2.637689 -Inf  Inf 0.8509916 0.2505686

``` r
checkresiduals(PoetryForecastETS) #  p-value <  0.001
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-6-6.png)<!-- -->

    ## 
    ##  Ljung-Box test
    ## 
    ## data:  Residuals from ETS(A,N,A)
    ## Q* = 85.722, df = 24, p-value = 0.000000007272
    ## 
    ## Model df: 0.   Total lags used: 24

``` r
# Остатки распределены нормально, белый шум отсутствует
poetry_ETS_f<-forecast(PoetryForecastETS, h = 12)


# Модель ARIMA 
PoetryForecastARIMA <- auto.arima(ts_poets_mean, d=1, D=1, 
                                  stepwise = FALSE, approximation = FALSE, 
                                  trace = F) 
summary(PoetryForecastARIMA) # Residual SD: 5,696
```

    ## Series: ts_poets_mean 
    ## ARIMA(0,1,4)(0,1,1)[12] 
    ## 
    ## Coefficients:
    ##           ma1      ma2      ma3      ma4     sma1
    ##       -0.5435  -0.1447  -0.0005  -0.2119  -0.6235
    ## s.e.   0.0718   0.0877   0.1053   0.0743   0.0579
    ## 
    ## sigma^2 = 14.99:  log likelihood = -611.49
    ## AIC=1234.97   AICc=1235.37   BIC=1255.34
    ## 
    ## Training set error measures:
    ##                      ME     RMSE      MAE  MPE MAPE      MASE        ACF1
    ## Training set -0.1884959 3.718872 2.259301 -Inf  Inf 0.7289133 -0.03030932

``` r
checkresiduals(PoetryForecastARIMA) #  p-value <  0.001 
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-6-7.png)<!-- -->

    ## 
    ##  Ljung-Box test
    ## 
    ## data:  Residuals from ARIMA(0,1,4)(0,1,1)[12]
    ## Q* = 33.452, df = 19, p-value = 0.02131
    ## 
    ## Model df: 5.   Total lags used: 24

``` r
# Остатки распределены нормально, белый шум отсутствует
poetry_ARIMA_f<-forecast(PoetryForecastARIMA, h = 12)


# Вновь выбираем ARIMA

# Проверка прогноза
summary(prosa_ARIMA_f)
```

    ## 
    ## Forecast method: ARIMA(1,1,2)(2,1,0)[12]
    ## 
    ## Model Information:
    ## Series: ts_proza_mean 
    ## ARIMA(1,1,2)(2,1,0)[12] 
    ## 
    ## Coefficients:
    ##           ar1     ma1      ma2     sar1     sar2
    ##       -0.7356  0.0336  -0.8472  -0.4303  -0.3634
    ## s.e.   0.0594  0.0423   0.0387   0.0837   0.0884
    ## 
    ## sigma^2 = 31.73:  log likelihood = -690.52
    ## AIC=1393.04   AICc=1393.44   BIC=1413.38
    ## 
    ## Error measures:
    ##                      ME     RMSE      MAE        MPE     MAPE      MASE
    ## Training set -0.0939252 5.410097 3.603215 -0.5844654 10.20951 0.8798083
    ##                    ACF1
    ## Training set 0.03787563
    ## 
    ## Forecasts:
    ##          Point Forecast    Lo 80    Hi 80    Lo 95    Hi 95
    ## May 2023       29.19369 21.97467 36.41271 18.15316 40.23423
    ## Jun 2023       26.05598 18.52320 33.58875 14.53559 37.57636
    ## Jul 2023       24.90207 17.36556 32.43857 13.37597 36.42816
    ## Aug 2023       25.12905 17.44080 32.81729 13.37089 36.88720
    ## Sep 2023       25.61518 17.92357 33.30679 13.85188 37.37848
    ## Oct 2023       29.28036 21.49903 37.06168 17.37984 41.18087
    ## Nov 2023       33.34781 25.55176 41.14385 21.42478 45.27083
    ## Dec 2023       32.28221 24.42313 40.14128 20.26279 44.30162
    ## Jan 2024       43.08393 35.20085 50.96702 31.02779 55.14007
    ## Feb 2024       44.82036 36.88682 52.75389 32.68706 56.95365
    ## Mar 2024       34.73075 26.76740 42.69410 22.55186 46.90964
    ## Apr 2024       32.18707 24.17963 40.19451 19.94075 44.43339

``` r
# Визуализация

options(scipen=10000)
autoplot(poetry_ARIMA_f) + 
  ggtitle("Предсказание по месяцам") +
  theme(plot.title = element_text(hjust = 0.5)) + xlab("")
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-6-8.png)<!-- -->

### Автокорреляции

``` r
#Расчет автокорреляции

#Загружаем пакет
library (feasts)

#Выполняем вычисления для сдвигов
avtocor <- acf(ts_proza_mean, pl = FALSE)

#Строим коррелограмму временного ряда для прозы
acf(ts_proza_mean)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
#График имеет закономерности, но все же лаги иногда не совпадают

avtocor2 <- acf(ts_poets_mean, pl = FALSE)
#Строим коррелограмму временного ряда для поэзии
acf(ts_poets_mean)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

В обоих графах есть тренд, сезонное колебание данных Лаги убывают и
возрастают закономерно

### Нейросети

Продолжим предсказание будущих значений, но уже с помощью готовых
нейросетей для анализа временных рядов.

NNAR-Neural Network Autoregression Model - имеет две составляющие, p
обозначает количество запаздывающих значений, которые используются в
качестве входных данных k обозначает количество скрытых узлов

Если набор данных сезонный, то и обозначения очень похожи NNAR(p,P,k), p
подбирается по AIC, а P обозначает количество сезонных лагов

``` r
library(tsibble)  #для работы с рядами по принципам tidy data
library(fable)  #для tidy прогнозирования
library(forecast)  #для функции forecast и сети nnetar
library(nnfor) #для функции mlp


set.seed(42)

nnetar_fit_poets <- nnetar(ts_poets_mean)
nnetar_pred_poets <- forecast(nnetar_fit_poets, h = 12, PI = F)
autoplot(nnetar_pred_poets) + theme(plot.title = element_text(hjust = 0.5))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
nnetar_fit_poets
```

    ## Series: ts_poets_mean 
    ## Model:  NNAR(16,1,8)[12] 
    ## Call:   nnetar(y = ts_poets_mean)
    ## 
    ## Average of 20 networks, each of which is
    ## a 16-8-1 network with 145 weights
    ## options were - linear output units 
    ## 
    ## sigma^2 estimated as 0.3599

``` r
nnetar_fit_proza <- nnetar(ts_proza_mean)
nnetar_pred_proza <- forecast(nnetar_fit_proza, h = 12, PI = F)
autoplot(nnetar_pred_proza) + theme(plot.title = element_text(hjust = 0.5))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->

``` r
nnetar_fit_proza
```

    ## Series: ts_proza_mean 
    ## Model:  NNAR(13,1,7)[12] 
    ## Call:   nnetar(y = ts_proza_mean)
    ## 
    ## Average of 20 networks, each of which is
    ## a 13-7-1 network with 106 weights
    ## options were - linear output units 
    ## 
    ## sigma^2 estimated as 1.488

MLP - multilayer perceptrons Автоматически считывает входные данные
авторегрессии и делает необходимую предварительную обработку временного
ряда. По умолчанию обучает 20 сетей, которые используются для создания
ансамблевого прогноза. В конце по умолчанию дает медианное значение
прогнозов. Имеет один скрытый слой с 5 узлами. По желанию можно
переопределить любой из параметров.

``` r
mlp_fit_poets <- mlp(ts_poets_mean)
plot(mlp_fit_poets ) # так выглядит сеть (архитектура). Розовые входные данные - бинарные dummies (1-T, 0-F), используемые для кодирования сезонности, а серые — авторегрессионные лаги
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

``` r
print(mlp_fit_poets )
```

    ## MLP fit with 5 hidden nodes and 20 repetitions.
    ## Series modelled in differences: D1.
    ## Univariate lags: (1,2,3,4,5,6,7,8,9,10,11)
    ## Deterministic seasonal dummies included.
    ## Forecast combined using the median operator.
    ## MSE: 0.8945.

``` r
mlp_pred_poets <- forecast(mlp_fit_poets)
autoplot(mlp_pred_poets) + theme(plot.title = element_text(hjust = 0.5))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-9-2.png)<!-- -->

``` r
mlp_fit_proza <- mlp(ts_proza_mean)
plot(mlp_fit_proza ) 
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-9-3.png)<!-- -->

``` r
print(mlp_fit_proza )
```

    ## MLP fit with 5 hidden nodes and 20 repetitions.
    ## Series modelled in differences: D1.
    ## Univariate lags: (1,2,3,4,5,6,7,8,9,10,11)
    ## Deterministic seasonal dummies included.
    ## Forecast combined using the median operator.
    ## MSE: 1.6805.

``` r
mlp_pred_proza <- forecast(mlp_fit_proza)
autoplot(mlp_pred_proza) + theme(plot.title = element_text(hjust = 0.5))
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-9-4.png)<!-- -->

``` r
#Можно настроить количество скрытых узлов, но мы не будем. Для этого есть разные варианты, но все требуют больших вычислительных ресурсов.
```

Оценим и сравним точность всех моделей, примененных при анализе.
Основным параметром для сравнения будет MAE - средняя абсолютная ошибка
(модули разностей предсказанного и реального суммируются и делятся на
число наблюдений) Чем она меньше - тем лучше

``` r
all_models_poets=list(PoetryForecastNaive,PoetryForecastETS,PoetryForecastARIMA ,nnetar_fit_poets,mlp_pred_poets)
row_n=c("Naive","ETS","ARIMA","NNETAR","MLP")
all_poets_acc <- data.frame()
for (mod in all_models_poets){
  ac=as.data.frame(accuracy(mod))
  all_poets_acc=rbind(all_poets_acc,ac)
}  
row.names(all_poets_acc)=row_n
all_poets_acc
```

    ##                  ME      RMSE       MAE       MPE    MAPE      MASE        ACF1
    ## Naive   0.021212121 5.1909984 3.2515152       NaN     Inf 1.0000000 -0.38647104
    ## ETS    -0.081256099 4.0565277 2.6376890      -Inf     Inf 0.8509916  0.25056857
    ## ARIMA  -0.188495916 3.7188715 2.2593014      -Inf     Inf 0.7289133 -0.03030932
    ## NNETAR -0.007633451 0.5999088 0.4404059 -0.403826 2.61615 0.1420872  0.15919434
    ## MLP    -0.002959786 0.9458062 0.6543785       Inf     Inf 0.2038228  0.09993709

``` r
plot(ts_poets_mean, main = "Модели прогнозиования вр. рядов по поэтам")
lines(poetry_Naive_f$mean, col = "red")
lines(poetry_ETS_f$mean, col = "blue")
lines(poetry_ARIMA_f$mean, col = "green")
lines(nnetar_pred_poets$mean, col = "violet")
lines(mlp_pred_poets$mean, col = "orange")
legend("topleft", row_n, col = c("red", "blue", "green","violet","orange"), lty = 1)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
all_models_proza=list(ProsaForecastNaive,ProsaForecastETS,ProsaForecastARIMA ,nnetar_fit_proza,mlp_pred_proza)
row_n=c("Naive","ETS","ARIMA","NNETAR","MLP")
all_proza_acc <- data.frame()
for (mod in all_models_proza){
  ac=as.data.frame(accuracy(mod))
  all_proza_acc=rbind(all_proza_acc,ac)
}  
row.names(all_proza_acc)=row_n
all_proza_acc
```

    ##                  ME     RMSE       MAE         MPE      MAPE      MASE
    ## Naive   0.062404871 8.026244 4.9817352        -Inf       Inf 1.0000000
    ## ETS    -0.611682146 6.282672 4.0316880 -4.38563338 12.711875 0.9844299
    ## ARIMA  -0.093925197 5.410097 3.6032148 -0.58446543 10.209513 0.8798083
    ## NNETAR  0.005606528 1.219660 0.9197633 -0.27571303  2.648447 0.2245815
    ## MLP    -0.007939415 1.296324 0.9258794  0.01805223  2.619287 0.2206833
    ##                ACF1
    ## Naive  -0.359367731
    ## ETS     0.241945845
    ## ARIMA   0.037875630
    ## NNETAR  0.004879543
    ## MLP    -0.027087808

``` r
plot(ts_proza_mean, main = "Модели прогнозиования вр. рядов по прозаикам")
lines(prosa_Naive_f$mean, col = "red")
lines(prosa_ETS_f$mean, col = "blue")
lines(prosa_ARIMA_f$mean, col = "green")
lines(nnetar_pred_proza$mean, col = "violet")
lines(mlp_pred_proza$mean, col = "orange")
legend("topleft", row_n, col = c("red", "blue", "green","violet","orange"), lty = 1)
```

![](Berdiyeva_HW_bungo_github_files/figure-gfm/unnamed-chunk-10-2.png)<!-- -->

Как видим, прогнозы, основанные на модели Naive, сработали не совсем
адекватно. А все остальные довольно похожи и лишь немного отличаются
степенью детализации колебаний и оптимистичностью

Наиболее точной моделью в обоих случаях является NNETAR.
