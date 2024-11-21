# day46_Top 100 billboard songs on your special day

- Show the code
    
    ```python
    from bs4 import BeautifulSoup
    import requests
    import re
    import spotipy
    from spotipy.oauth2 import SpotifyOAuth
    import os
    from pprint import pprint
    import smtplib
    
    ### SECRET
    CLIENT_ID = os.environ['client_id']
    CLIENT_SECRET = os.environ['client_secret']
    RE_DIR_URI = os.environ['re_dir_uri']
    EMAIL_ADDR = os.environ['user_email_addr']
    EMAIL_PWD = os.environ['user_email_pwd']
    
    ### BASIC INFORMATION INPUT
    URL = ''
    while True:
        travel_back_to = input('Which year do you want to travel to? Type the dat in this format YYYY-MM-DD: ')
        if re.match(r'^\d{4}-\d{2}-\d{2}$', travel_back_to):
            URL = f'https://www.billboard.com/charts/hot-100/{travel_back_to}'
            break
        else:
            print('Wrong date format, please re-enter again.')
    user_name = input('What is your name?')
    while True:
        user_email = input('What is your email? I will send you the list of Top 100 song and Spotify playlist on a date you choose.')
        if re.match(r'^[\w\.-]+@[\w\.-]+\.\w+$', user_email):
            break
        else:
            print('Wrong email format, please re=enter again.')
    
    ###Making the soup
    respond = requests.get(url=URL)
    top_100_songs = respond.text
    soup = BeautifulSoup(top_100_songs, 'html.parser')
    
    ###Find track title and artist
    titles = soup.select('div li ul li h3')
    artists = soup.select('div ul li ul li span')
    
    ##ADD all tracks and artists into separate lists
    all_tracks = [title.get_text().strip() for title in titles]
    all_artists_temp = [artist.get_text().strip() for artist in artists]
    
    all_artists = []
    for i in range(0, len(all_artists_temp) - 1, 7):
        all_artists.append(all_artists_temp[i])
    
    print(f'Total number of songs found: {len(all_tracks)}')
    print(f'Total number of artists found: {len(all_artists)}')
    
    ##Saving the Top 100 song into txt format
    with open(f'top 100 song on {travel_back_to} for {user_name}', 'w') as file:
        for i in range(0, len(all_tracks) - 1, 1):
            file.write(f'{i + 1}. {all_tracks[i]} - {all_artists[i]}\n')
    
    ##spotify login information
    sp = spotipy.Spotify(auth_manager=SpotifyOAuth(
                                        client_id=CLIENT_ID,
                                        client_secret=CLIENT_SECRET,
                                        redirect_uri=RE_DIR_URI,
                                        scope='playlist-modify-private',
      open_browser=False,
      cache_path='.spotifycache'
    ))
    
    ###Find all URIs for next step to add into the playlist
    track_uris = []
    for i in range(0, len(all_tracks) - 1, 1):
        search = sp.search(q=f'track: {all_tracks[i]} artists: {all_artists[i]}', type='track')
    
        try:
            track_uri = search['tracks']['items'][0]['uri']
            # print(track_uri)
            track_uris.append(track_uri)
        except IndexError:
            print(f'Song: {all_tracks[i]} -> Not found.')
    
    ###Create playlist
    user_id = sp.current_user()['id']
    playlist_name = f'{travel_back_to} Billboard 100 for {user_name}'
    playlist_description = f'A playlist create to retrack a special date on {travel_back_to} for {user_name}'
    playlist_for_special_date = sp.user_playlist_create(user=user_id,
                                                        name=playlist_name,
                                                        public=False,
                                                        collaborative=False,
                                                        description=playlist_description)
    
    ##Add all tracks into the playlist
    playlist_id = playlist_for_special_date['id']
    
    track_added_playlist = sp.playlist_add_items(playlist_id=playlist_id, items=track_uris)
    
    ##Send email to user with Spotify playlist URL and the whole list.
    with open(f'top 100 song on {travel_back_to} for {user_name}') as file:
        songs = file.read()
        print(songs)
    
    with smtplib.SMTP("smtp.gmail.com") as connection:
        connection.starttls()
        connection.login(user=EMAIL_ADDR, password=EMAIL_PWD)
        connection.sendmail(from_addr=EMAIL_ADDR,
                            to_addrs=user_email,
                            msg=f"Subject:Top 100 Songs on {travel_back_to} for {user_name}\n\n Spotify playlist just for you: {playlist_for_special_date['external_urls']['spotify']}\n{songs}")
    ```
    
- Result in Console:
    
    ```python
    Which year do you want to travel to? Type the dat in this format YYYY-MM-DD: 1991-08-11
    What is your name?Adam
    What is your email? I will send you the list of Top 100 song and Spotify playlist on a date you choose.Adam_CJH@Outlook.com
    1. (Everything I Do) I Do It For You - Bryan Adams
    2. Every Heartbeat - Amy Grant
    3. It Ain't Over 'til It's Over - Lenny Kravitz
    4. Summertime - D.J. Jazzy Jeff & The Fresh Prince
    5. Fading Like A Flower (Every Time You Leave) - Roxette
    6. Temptation - Corina
    7. P.A.S.S.I.O.N. - Rhythm Syndicate
    8. I'll Be There - The Escape Club
    9. Wind Of Change - Scorpions
    10. 3 A.M. Eternal - The KLF
    11. I Can't Wait Another Minute - Hi-Five
    12. The Promise Of A New Day - Paula Abdul
    13. Motownphilly - Boyz II Men
    14. Crazy - Seal
    15. Things That Make You Go Hmmmm. . . - C+C Music Factory
    16. Unforgettable - Natalie Cole
    17. Love And Understanding - Cher
    18. Right Here, Right Now - Jesus Jones
    19. Too Many Walls - Cathy Dennis
    20. Time, Love And Tenderness - Michael Bolton
    21. Now That We Found Love - Heavy D & The Boyz
    22. The Motown Song - Rod Stewart
    23. I Adore Mi Amor - Color Me Badd
    24. Unbelievable - EMF
    25. Good Vibrations - Marky Mark & The Funky Bunch Featuring Loleatta Holloway
    26. Hard To Handle - The Black Crowes
    27. Love Of A Lifetime - Firehouse
    28. Only Time Will Tell - Nelson
    29. My Name Is Not Susan - Whitney Houston
    30. You Could Be Mine - Guns N' Roses
    31. Just Like You - Robbie Nevil
    32. Rush Rush - Paula Abdul
    33. Learning To Fly - Tom Petty And The Heartbreakers
    34. Shiny Happy People - R.E.M.
    35. It Hit Me Like A Hammer - Huey Lewis & The News
    36. Pop Goes The Weasel - 3rd Bass
    37. Place In This World - Michael W. Smith
    38. Piece Of My Heart - Tara Kemp
    39. Something To Talk About - Bonnie Raitt
    40. Love On A Rooftop - Desmond Child
    41. Everybody Plays The Fool - Aaron Neville
    42. The Dream Is Still Alive - Wilson Phillips
    43. Do You Want Me - Salt-N-Pepa
    44. Got A Love For You - Jomanda
    45. Hole Hearted - Extreme
    46. Let The Beat Hit 'Em - Lisa Lisa And Cult Jam
    47. Do Anything - Natural Selection
    48. The Sound Of Your Voice - 38 Special
    49. Here I Am (Come And Take Me) - UB40
    50. I Wanna Sex You Up (From "New Jack City") - Color Me Badd
    51. Nights Like This (From "The Five Heartbeats") - After 7
    52. Romantic - Karyn White
    53. With You - Tony Terry
    54. My Fallen Angel - Coro
    55. More Than Words - Extreme
    56. There She Goes - The La's
    57. Love. . .Thy Will Be Done - Martika
    58. I'll Never Let You Go - Steelheart
    59. The Truth - Tami Show
    60. That's The Way Love Goes - Young M.C.
    61. Lily Was Here - David A. Stewart Introducing Candy Dulfer
    62. Don't Want To Be A Fool - Luther Vandross
    63. The One And Only - Chesney Hawkes
    64. Elevate My Mind - Stereo MC's
    65. Latin Active - Lighter Shade Of Brown Featuring Teardrop & Shiro
    66. Gett Off - Prince And The N.P.G.
    67. Running Back To You - Vanessa Williams
    68. Can You Stop The Rain - Peabo Bryson
    69. Walking In Memphis - Marc Cohn
    70. Can't Forget You - Gloria Estefan
    71. Till Somebody Loves You - Henry Lee Summer
    72. Power Of Love/Love Power - Luther Vandross
    73. Low Rider (On The Boulevard) - Latin Alliance Featuring War
    74. Just Want To Hold You - Jasmine Guy
    75. Silver Thunderbird - Marc Cohn
    76. Turn It Up - Oaktown's 3.5.7
    77. If I Say - Kingofthehill
    78. Kiss Them For Me - Siouxsie & The Banshees
    79. Get Serious - Cut 'N' Move
    80. I'm Not Your Puppet - Hi-C
    81. Losing My Religion - R.E.M.
    82. How Can I Ease The Pain - Lisa Fischer
    83. Chorus - Erasure
    84. Power Windows - Billy Falcon
    85. Louder Than Love - TKA
    86. Wishing On The Same Star - Keedy
    87. Hey Stoopid - Alice Cooper
    88. Just Ask Me To (From "Boyz N The Hood") - Tevin Campbell
    89. This Beat Is Hot - B.G. The Prince Of Rap
    90. Face The Music - RTZ
    91. Kissing You - Keith Washington
    92. When Love Cries - Donna Summer
    93. After The Summer's Gone - PC Quest
    94. A Better Love - Londonbeat
    95. I Don't Wanna See You - Michael Morales
    96. Dirty Love - Thunder
    97. Gypsy Woman (She's Homeless) - Crystal Waters
    98. Never Gonna Let You Down - Surface
    99. My Body Says Yes - Titiyo
    ```
    
- Email user receive
    
    ![Untitled](Untitled%2016.png)
    
- Spotify playlist shared with the user. It can be found in the email.
    
    ![Untitled](Untitled%2017.png)