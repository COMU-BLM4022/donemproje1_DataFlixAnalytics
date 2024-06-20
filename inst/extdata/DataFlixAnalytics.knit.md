---
title:"Bilgi Yönetim Sistemleri - Bitirme Projesi Rapor:
author:
  -"Bedran Can Sarı-200401023"
  -"Ali Deniz Yakar-200401018"
  -"Fatih Alpaslan Güzel-200401055"
date: "2024-05-27"
output: html_document
---
Q1 : İş Probleminin tanımı

Bu rapor, Netflix firmasının ürünlerinin IMDB puanlarını analiz ederek gelecek planlarıyla ilgili öneride bulunup yol haritası çizmelerine katkıda bulunmayı amaçlayarak tasarlanmıştır. İş problemi içeriklerin türlerini, yaş kategorilerini, çıkış yıllarını vs. gibi başlıkları değerlendirerek en yüksek puanlı içerikleri analiz etmek ve Netflix'in bu yönde projeler çıkarmasını sağlamaktır. Bu amaçla, ERPNext adlı açık kaynaklı ve esnek bir ERP(Enterprise Resoruce Planning) platformu kullanılmaktadır.

##Q2: İş problemini çözmede kullanılacak data seti
#### Veri Setinin Detayları
Veri Seti Adı:
Netflix IMDB Scores

Veri Seti Adresi:
https://www.kaggle.com/datasets/thedevastator/netflix-imdb-scores

İçerik:
Netflix IMDB Scores veriseti, Netflix'te bulunan çeşitli içerikler hakkında kapsamlı bilgiler sağlar. Veri kümesi, başlığın adı, türü (TV şovu veya film olup olmadığı), içeriğin kısa bir açıklaması, yayınlandığı yıl, yaş sertifikası derecelendirmesi, çalışma süresi (TV şovları için: bölümlerin uzunluğu) gibi ayrıntıları içerir. ; filmler için: süre), IMDb puanı ve IMDb oyları.

1.index:Veri setindeki her bir satırı benzersiz şekilde tanımlayan numara veya sıra. Genellikle sıfırdan başlayan bir dizin kullanılır.
2.id: Her bir verinin benzersiz kimliği. Genellikle veri tabanlarında veya veri setlerinde her kaydın tanımlanması için kullanılır.
3.title: Filmin veya dizinin adı.
4.type: Verinin türü. Bu, "MOVIE" veya "TV SHOW" gibi kategorileri belirler.
5.description: Filmin veya dizinin kısa özeti veya tanıtımı.
6.release_year: Filmin veya dizinin yayımlandığı yıl.
7.age_certification: Filmin veya dizinin yaş sınıflandırması. Örneğin, "PG", "R" gibi.
8.runtime:Filmin veya dizinin toplam süresi, genellikle dakika cinsinden.
9.imdb_id: Filmin veya dizinin IMDb üzerindeki benzersiz kimliği.
10.imdb_score: Filmin veya dizinin IMDb üzerindeki puanı.
11.imdb_votes: Filmin veya dizinin IMDb üzerindeki oy sayısı.

Verinin Üretildiği Şartlar:
Veri seti, doğruluk ve alaka düzeyini sağlamak için dikkatle seçilmiştir. Veri bütünlüğünü korumak için IMDb puanlarındaki boş değerleri hariç tutar. Her giriş ayrıca JustWatch'a (yasal yayın için bir platform) karşılık gelen bir kimliğin yanı sıra IMDb'deki ilgili başlık kimliğini de içerir.

Veri Formatı:
Veri seti CSV formatındadır.

Veri Boyutu:
716 kB
output: html_document
---


## Q3: Veri seti keşifsel analizi 


```r
knitr::opts_chunk$set(echo = TRUE)
library(readr)
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(ggplot2)
library(magrittr)

dosya_yolu <- "NetflixTVShowsandMovies.csv"
data <- read.csv(dosya_yolu)

# veri setinin bir kısmını inceliyoruz
head(data)
```

```
##   index       id                           title  type
## 1     0  tm84618                     Taxi Driver MOVIE
## 2     1 tm127384 Monty Python and the Holy Grail MOVIE
## 3     2  tm70993                   Life of Brian MOVIE
## 4     3 tm190788                    The Exorcist MOVIE
## 5     4  ts22164    Monty Python's Flying Circus  SHOW
## 6     5  tm14873                     Dirty Harry MOVIE
##                                                                                                                                                                                                                                                                                                                                                                                                                                                          description
## 1                                                                                                                                                                                                                                A mentally unstable Vietnam War veteran works as a night-time taxi driver in New York City where the perceived decadence and sleaze feed his urge for violent action, attempting to save a preadolescent prostitute in the process.
## 2                                    King Arthur, accompanied by his squire, recruits his Knights of the Round Table, including Sir Bedevere the Wise, Sir Lancelot the Brave, Sir Robin the Not-Quite-So-Brave-As-Sir-Lancelot and Sir Galahad the Pure. On the way, Arthur battles the Black Knight who, despite having had all his limbs chopped off, insists he can still fight. They reach Camelot, but Arthur decides not  to enter, as "it is a silly place".
## 3 Brian Cohen is an average young Jewish man, but through a series of ridiculous events, he gains a reputation as the Messiah. When he's not dodging his followers or being scolded by his shrill mother, the hapless Brian has to contend with the pompous Pontius Pilate and acronym-obsessed members of a separatist movement. Rife with Monty Python's signature absurdity, the tale finds Brian's life paralleling Biblical lore, albeit with many more laughs.
## 4                                                                                                                12-year-old Regan MacNeil begins to adapt an explicit new personality as strange events befall the local area of Georgetown. Her mother becomes torn between science and superstition in a desperate bid to save her daughter, and ultimately turns to her last hope: Father Damien Karras, a troubled priest who is struggling with his own faith.
## 5                                                                                                                                                                                                                                                                                            A British sketch comedy series with the shows being composed of surreality, risquÃ© or innuendo-laden humour, sight gags and observational sketches without punchlines.
## 6                                                                When a madman dubbed 'Scorpio' terrorizes San Francisco, hard-nosed cop, Harry Callahan â€“ famous for his take-no-prisoners approach to law enforcement â€“ is tasked with hunting down the psychopath. Harry eventually collars Scorpio in the process of rescuing a kidnap victim, only to see him walk on technicalities. Now, the maverick detective is determined to nail the maniac himself.
##   release_year age_certification runtime   imdb_id imdb_score imdb_votes
## 1         1976                 R     113 tt0075314        8.3     795222
## 2         1975                PG      91 tt0071853        8.2     530877
## 3         1979                 R      94 tt0079470        8.0     392419
## 4         1973                 R     133 tt0070047        8.1     391942
## 5         1969             TV-14      30 tt0063929        8.8      72895
## 6         1971                 R     102 tt0066999        7.7     153463
```


```r
#Type'a göre IMDb ortalamasını gösteren bar grafiği:
imdb_avg_by_type <- data %>%
  group_by(type) %>%
  summarise(avg_imdb_score = mean(imdb_score, na.rm = TRUE))

ggplot(imdb_avg_by_type, aes(x = type, y = avg_imdb_score, fill = type)) +
  geom_bar(stat = "identity") +
  theme_minimal() +
  labs(title = "Type'a Göre IMDb Ortalaması",
       x = "Type",
       y = "Ortalama IMDb Puani")
```

<img src="DataFlixAnalytics_files/figure-html/unnamed-chunk-2-1.png" width="672" />


```r
#Release year'a göre IMDb ortalamasını gösteren çizgi grafiği:
imdb_avg_by_year <- data %>%
  group_by(release_year) %>%
  summarise(avg_imdb_score = mean(imdb_score, na.rm = TRUE))

ggplot(imdb_avg_by_year, aes(x = release_year, y = avg_imdb_score)) +
  geom_line(color = "blue") +
  geom_point() +
  theme_minimal() +
  labs(title = "Yıla Göre IMDb Ortalaması",
       x = "Yıl",
       y = "Ortalama IMDb Puanı")
```

<img src="DataFlixAnalytics_files/figure-html/unnamed-chunk-3-1.png" width="672" />



```r
#IMDb puanlarına göre filmlerin dağılımını gösteren histogram
ggplot(data, aes(x = imdb_score)) +
  geom_histogram(binwidth = 0.5, fill = "blue", color = "black") +
  theme_minimal() +
  labs(title = "IMDb Puanlarının Dağılımı",
       x = "IMDb Puani",
       y ="FilmSayisi")
```

<img src="DataFlixAnalytics_files/figure-html/unnamed-chunk-4-1.png" width="672" />




```r
#Yaş sertifikasyonlarına göre film sayısını gösteren pasta grafiği:
age_certification_count <- data %>%
  group_by(age_certification) %>%
  summarise(count = n())

ggplot(age_certification_count, aes(x = "", y = count, fill = age_certification)) +
  geom_bar(stat = "identity", width = 1) +
  coord_polar(theta = "y") +
  theme_minimal() +
  labs(title = "Yaş Sertifikasyonlarına Göre Film Sayısı")
```

<img src="DataFlixAnalytics_files/figure-html/unnamed-chunk-5-1.png" width="672" />
##Q4: Veri setinin BYS platformuna ithali
Veri setini ERPNext platformuna entegre etmek için izlenen adımlar aşağıda detaylı bir şekilde açıklanmıştır. Bu işlemde ERPNext'in RESTful API'si kullanılmış ve veri seti CSV formatından platforma aktarılmıştır.

Veri setini ERPNext platformuna entegre etmek için izlenen adımlar aşağıda detaylı bir şekilde açıklanmıştır. Bu işlemde ERPNext'in RESTful API'si kullanılmış ve veri seti CSV formatından platforma aktarılmıştır.

#### Adımlar

1. *ERPNext Kurulumu:*
   - Docker kullanılarak ERPNext platformu kuruldu.
   - Docker konteynerında ERPNext'in çalışır hale getirilmesi sağlandı.

2. *Veri Setinin Hazırlanması:*
   - Kaggle'dan indirilen CSV dosyası gerekli temizlik ve dönüştürme işlemleri yapılarak uygun hale getirildi.
   - Gereksiz sütunlar kaldırıldı, tarih formatları dönüştürüldü ve eksik veriler temizlendi.

3. *Veri Setinin ERPNext'e Aktarılması:*
   - ERPNext'in sağladığı panel kullanılarak CSV dosyasındaki veriler platforma aktarıldı.


```r
# ERPNext'ten veri çekilmesi

# Gerekli kütüphaneleri yükleyin
library(httr)
library(jsonlite)

# ERPNext API URL'i (newdoc için)
url <- "http://localhost:8080/api/resource/Movies/1"

api_key <- "f22b8052e893917"
api_secret <- "8783294f64b514d"

# GET isteği yapın
#response <- GET(url, authenticate(Administrator, admin, type = "basic"))

# GET isteğini yap
response <- GET(url, authenticate(api_key, api_secret, type = "basic"))

# Yanıtı kontrol et ve verileri al
if (status_code(response) == 200) {
  data <- content(response, as = "text")
  json_data <- fromJSON(data)
} else {
  cat("İstek başarısız oldu. Durum kodu:", status_code(response), "\n")
  cat(content(response, as = "text"), "\n")  # Hata mesajını yazdır
}
```

```
## İstek başarısız oldu. Durum kodu: 404
```

```
## No encoding supplied: defaulting to UTF-8.
```

```
## {"exc_type":"DoesNotExistError","_server_messages":"[\"{\\\"message\\\": \\\"Movies 1 not found\\\", \\\"title\\\": \\\"Message\\\", \\\"indicator\\\": \\\"red\\\", \\\"raise_exception\\\": 1, \\\"__frappe_exc_id\\\": \\\"f1f2ddac90ca0304ca4856e86f6e7ed4d00b76488609774cf8e32c4e\\\"}\"]"}
```
## Q6 : Çalışan platformun gösterildiği sesli anlatımlı video
[Video](https://www.youtube.com/watch?v=tWTeM75M6tA)
