# Seasons

Let's go get seasons of various leagues, and insert it into the DB.

```sh
# NHL Season Loader
curl -s 'https://statsapi.web.nhl.com/api/v1/seasons' | jq '.seasons' | sqlite-utils insert hockey.db nhl_seasons --pk=seasonId --ignore -
```

```sh
for i in {2020..2023}
do
  b=$(($i + 1))
  season=$i$b
  curl -s "https://statsapi.web.nhl.com/api/v1/schedule?season=$season" | jq '[.dates[].games[] | {
    gameId: .gamePk, 
    gameType: .gameType,
    season: .season,
    gameDate: .gameDate,
    awayTeamId: .teams.away.team.id, 
    awayTeamName: .teams.away.team.name,
    awayTeamScore: .teams.away.score,
    homeTeamId: .teams.home.team.id, 
    homeTeamName: .teams.home.team.name,
    homeTeamScore: .teams.home.score,
    venue: .venue.name
  }]' | sqlite-utils insert hockey.db nhl_games --pk=gameId --ignore -
  sleep 2
done
```

```sh
sqlite3 hockey.db "select count(*) from nhl_games;"
```

```sh

```