# day45_Top 100 Movies Web scraping

```python
###Requirement
## 100 Movies that You Must Watch

# Objective

Scrape the top 100 movies of all time from a website. Generate a text file called `movies.txt` that lists the movie titles in ascending order (starting from 1). 
The result should look something like this:

```
1) The Godfather
2) The Empire Strikes Back
3) The Dark Knight
4) The Shawshank Redemption
... and so on
```
The central idea behind this project is to be able to use BeautifulSoup to obtain some data - like movie titles - from a website like Empire's (or from, say Timeout or Stacker that have curated similar lists).
```

main.py

```python
import requests as re
from bs4 import BeautifulSoup

URL = "https://web.archive.org/web/20200518073855/https://www.empireonline.com/movies/features/best-movies-2/"

# Write your code below this line 👇
respond = re.get(url=URL)
top_100_movie = respond.text
soup = BeautifulSoup(top_100_movie, 'html.parser')
titles = soup.find_all(name='h3', class_='title')
all_titles = [str(title.text.split(") ")[-1]) for title in titles]
#print(all_titles)
#print(len(all_titles))

with open('movies.txt', 'w') as file:
    for i in range(len(all_titles), 0, -1):
        file.write(f"{i}) {all_titles[i - 1]}\n")
```

output: movies.txt

```
100) The Godfather
99) The Empire Strikes Back
98) The Dark Knight
97) The Shawshank Redemption
96) Pulp Fiction
95) Goodfellas
94) Raiders Of The Lost Ark
93) Jaws
92) Star Wars
91) The Lord Of The Rings: The Fellowship Of The Ring
90) Back To The Future
89) 12: The Godfather Part II
88) Blade Runner
87) Alien
86) Aliens
85) The Lord Of The Rings: The Return Of The King
84) Fight Club
83) Inception
82) Jurassic Park
81) Die Hard
80) 2001: A Space Odyssey
79) Apocalypse Now
78) The Lord Of The Rings: The Two Towers
77) The Matrix
76) Terminator 2: Judgment Day
75) Heat
74) The Good, The Bad And The Ugly
73) Casablanca
72) The Big Lebowski
71) Seven
70) Taxi Driver
69) The Usual Suspects
68) Schindler's List
67) Guardians Of The Galaxy
66) The Shining
65) The Departed
64) The Thing
63) Mad Max: Fury Road
62) Saving Private Ryan
61) 12 Angry Men
60) Eternal Sunshine Of The Spotless Mind
59) There Will Be Blood
58) One Flew Over The Cuckoo's Nest
57) Gladiator
56) Drive
55) Citizen Kane
54) Interstellar
53) The Silence Of The Lambs
52) Trainspotting
51) Lawrence Of Arabia
50) It's A Wonderful Life
49) Once Upon A Time In The West
48) Psycho
47) Vertigo
46) Pan's Labyrinth
45) Reservoir Dogs
44) Whiplash
43) Inglourious Basterds
42) E.T. â The Extra Terrestrial
41) American Beauty
40) Forrest Gump
39) La La Land
38) Donnie Darko
37) L.A. Confidential
36) Avengers Assemble
35) Return Of The Jedi
34) Memento
33) Ghostbusters
32) Singin' In The Rain
31) The Lion King
30) Hot Fuzz
29) Rear Window
28) Seven Samurai
27) Mulholland Dr.
26) Fargo
25) A Clockwork Orange
24) Toy Story
23) Oldboy
22) Captain America: Civil War
21) Spirited Away
20) The Social Network
19) Some Like It Hot
18) True Romance
17) Rocky
16) LÃ©on
15) Indiana Jones And The Last Crusade
14) Predator
13) The Exorcist
12) Shaun Of The Dead
11) No Country For Old Men
10) The Prestige
9) The Terminator
8) The Princess Bride
7) Lost In Translation
6) Arrival
5) Good Will Hunting
4) Titanic
3) Amelie
2) Raging Bull
1) Stand By Me
```