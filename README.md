# SoundCloud Data Crawling 
## Introduction
Data crawling from various web sources is a step in the data crawling process. Data crawling closely resembles the processes used by the top search engines. Data crawling, to put it simply, is a technique for locating web links and gathering information from them.
## Set Up
Before running make sure that you install all requirement libraries for this process following by below:
```
pip install -r setup.txt
```
## Crawling data by Selinium
- We would begin dynamically crawling data from the Soundcloud website using the Selinium driver, however it would take 1-2 days to achieve thousands of data samples. To save time, we ran a crawling procedure first, after which we put the data in a file called `SoundCloud data` folder.
- In case of user want to process yourself, you could uncomment this cell and run it directedly
```
driver = webdriver.Chrome()
cookies = False
playlists = {'id': [], 'title': [], 'author': [], 'tracks': [], 'likes': [], 'reposts': [], 'release': []}
tracks = {'id': [], 'title': [], 'author': [], 'plays': [], 'likes': [], 'reposts': [], 'release': []}
users = {'id': [], 'name': [], 'followers': [], 'following': [], 'tracks': []}
global playlist_id
global track_id
global user_id
playlist_id, track_id, user_id = 0, 0, 0
list_tracks_link, list_users_link = {}, {}
cookies_id = 'onetrust-accept-btn-handler'
time.sleep(1)
for i in range(ord('o'), ord('z')+1):
    elements = []
    key = chr(i)
    driver.get(f'https://soundcloud.com/search/sets?q={key}')
    if not(cookies):
        time.sleep(3)
        c = get_elements(driver, By.ID, cookies_id, delay=7, first=True).click()
        cookies = True
    while len(elements) < 50:
        scroll_down(driver)
        elements = driver.find_elements(By.CLASS_NAME, 'sound__content')
    get_playlists(driver, elements, playlists, tracks, list_users_link, list_tracks_link)
    get_info(driver, list_users_link, users)
    playlist_df = pd.DataFrame(playlists, columns=['id', 'title', 'author', 'tracks', 'likes', 'reposts', 'release'])
    track_df = pd.DataFrame(tracks, columns=['id', 'title', 'author', 'plays', 'likes', 'reposts', 'release'])
    user_df = pd.DataFrame(users, columns=['id', 'name', 'followers', 'following', 'tracks'])
    # Export to file
    playlist_df.to_csv('playlists.csv', index='id')
    track_df.to_csv('tracks.csv', index='id')
    user_df.to_csv('users.csv', index='id')
driver.quit()
```

## Preprocessing
- Prior to modeling and exploration, which we would do in this section using three separate main classes, we must first clean the data.
### User information

![Web capture_13-3-2023_175124_github com](https://user-images.githubusercontent.com/81562297/224681139-3166bbe0-8e18-453e-9e34-3b6664f661a0.jpeg)

### Track information

![Web capture_13-3-2023_175341_github com](https://user-images.githubusercontent.com/81562297/224681496-ecefa38f-e286-4de2-b5f1-ba40e5b27963.jpeg)

### Playlist informatin

![Web capture_13-3-2023_17565_github com](https://user-images.githubusercontent.com/81562297/224682150-6729438e-57c5-4d86-b689-64f90ce52141.jpeg)
![Web capture_13-3-2023_175624_github com](https://user-images.githubusercontent.com/81562297/224682160-4d878f8f-2db3-4b5c-a6c8-5a8ae6d2c060.jpeg)

## Visualazion and Mining
From the correlation matrix
- There is a strong correlation between playlist likes and reposts, as well as between total plays, playlist track likes, and reports. A track's play counts, likes, and reposts are all tied to one another. We can thus assume that the playlist including the songs with a lot of streams, likes, and reposts likewise has a lot of likes and reposts.
- There is no relation between an author's following and a specific number (likes, reposts, played). So, the uploaded tracks of an author have no effect on their followers.
- The year of the playlist release and the year of the music release are highly correlated. So, we may assume that the majority of the playlist was already formed when the tunes were first made available.
- The quantity of tracks uploaded and the amount of followers are not related. So, adding additional music to your uploads will not help you gain more followers.

![Web capture_13-3-2023_1801_github com](https://user-images.githubusercontent.com/81562297/224683002-7d1177d6-aafe-4053-901e-5bc17f6499aa.jpeg)

## References

