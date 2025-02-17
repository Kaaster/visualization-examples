### Источник данных
- https://mavenanalytics.io/data-playground

### Подготовка данных для дашборда
* 📂 spotify_history.csv → 📝 spotify_streaming_history_data_preparation.ipynb → 📂 [spotify_history.csv, spotify_songs.csv] → 📊 Power BI

### ER (power bi)
- spotify_history *-<-1 spotify_songs
- spotify_history *-<-1 _calendar

### Наиболее интересный DAX-код
1. Позиция артиста по кол-ву прослушиваний за период
```
played RANK OVER artist_name = 
RANKX(
    ALL(spotify_songs[artist_name]),
    [played],
    ,
    DESC,
    DENSE
)
```
2. Последняя новая песня за период, с добавлением артиста
```
last new song = 
VAR song =
CALCULATE(
    FIRSTNONBLANK(spotify_songs[track_name], 1),
    TOPN(1, FILTER(spotify_history, spotify_history[is_new] = TRUE()), spotify_history[ts], DESC)
)
VAR artist = 
CALCULATE(
    MAX(spotify_songs[artist_name]),
    spotify_songs[track_name] = song
)
RETURN 
CONCATENATE(song, CONCATENATE(" - ", artist))
```
