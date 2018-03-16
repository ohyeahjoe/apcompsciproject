# playlist-souffle
Breathe life into an old playlist by swapping out each track for another track on the same album or by the same artist.

Before souffle              |  After souffle (by album)
:--------------------------:|:-------------------------:
![](docs/jazz-singing.png)  |  ![](docs/jazz-singing-souffle.png)

### REST API

##### ```/refreshtoken``` POST
* Obtain a spotify refresh token and access token given a valid spotify authorization code
* Request
    * Headers:
        * ```Authorization: Bearer {AUTHORIZATION_CODE}```
* Response (200)
    * Body (application/json)
        * ```accessToken```: spotify access token
        * ```refreshToken```: spotify refresh token

##### ```/accesstoken``` POST
* Obtain a fresh spotify access token given a valid spotify refreshtoken
* Request
    * Headers:
        * ```Authorization: Bearer {REFRESH_TOKEN}```
* Response (200)
    * Body (application/json)
        * ```accessToken```: spotify access token

##### ```/souffle``` POST
* Create a souffle'd playlist from an original playlist in which each track on the souffle'd playlist is swapped with another song on the same album or by the same artist.
* Request
    * Headers:
        * ```Authorization: Bearer {ACCESS_TOKEN}```
    * Body (application/x-www-form-urlencoded)
        * ```playlistUri```: uri of the playlist to souffle
        * ```userId```: id of current user under whose account souffled playlist will be created
        * ```shuffleBy```: type of collection to shuffle by (```artist``` or ```album```)
* Response (201)
    * Headers:
        * ```Location: {SOUFFLED_PLAYLIST_URI}```

### Setting up virtual environment
```bash
# Create the virtual environment directory
virtualenv venv --python=python3

# Enter venv
source venv/bin/activate

# Install 
pip install -r requirements.txt
```

### [scripts/souffle_tester.py](scripts/souffle_tester.py)
```
usage: souffle_tester.py [-h] -a ACCESS_TOKEN [-l] -p PLAYLIST_URI -s
                         {artist,album} -u USER_ID

Souffle a playlist using the /souffle endpoint or a local lambda invocation

optional arguments:
  -h, --help            show this help message and exit
  -a ACCESS_TOKEN, --access-token ACCESS_TOKEN
                        Spotify access token. See -h for access token
                        instructions.
  -l, --local           If set, process the souffle request via a local lambda
                        invocation.
  -p PLAYLIST_URI, --playlist-uri PLAYLIST_URI
                        Uri of the playlist to be souffled. Original playlist
                        will not be altered.
  -s {artist,album}, --shuffle-by {artist,album}
                        Collection type to shuffle by.
  -u USER_ID, --user-id USER_ID
                        User id of account where souffled playlist will be
                        created.

Send a POST request to the /souffle endpoint to souffle a playlist. If the --local
flag is set, process the request via a local lambda invocation. On success, the
newly created playlist's uri is printed to stdout. Otherwise, an error message printed
to stderr.

* Obtaining a spotify accesstoken *
1. Visit "https://accounts.spotify.com/authorize?client_id=b231329aba1a4c539375436a267db917&response_type=code&redirect_uri=https://127.0.0.1:8100&scope=playlist-modify-public"
2. Enter your spotify username and password to grant playlist-modify-public scope.
3. Copy the authorization code returned as a url parameter: "/?code={AUTHORIZATION_CODE}"
   - Auth redirection will error, but the auth code can still be extracted from the url.
4. Use the scripts/refreshtoken.sh script to obtain a refresh token and access token
5. If your access token expires, use the scripts/accesstoken.sh script to obtain a fresh access token.
```

### Spotify authorization flow
For more information on the spotify authorization flow (i.e, auth codes, refresh tokens and access tokens, see: https://beta.developer.spotify.com/documentation/general/guides/authorization-guide/.
