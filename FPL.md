```python
# import sys
# !{sys.executable} -m pip install fpl
```


```python
import pandas as pd
import numpy as np
import json
import requests
from pandas.io.json import json_normalize
from sklearn.preprocessing import MinMaxScaler
import requests
from datetime import datetime as dt
import asyncio
from operator import attrgetter

import aiohttp
from prettytable import PrettyTable

from fpl import FPL
from fpl.utils import team_converter
from colorama import Fore, init
```


```python
session = requests.session()
url = 'https://users.premierleague.com/accounts/login/'
creds = ("knowingvivek@gmail.com", "Pl1nc#$$")
payload = {
    'password': creds[0],
    'login': creds[1],
    'redirect_uri': 'https://fantasy.premierleague.com/a/login',
    'app': 'plfpl-web'
}
session.post(url, data=payload)
global team_map
```


```python
def get_json(file_path):
    r = requests.get('https://fantasy.premierleague.com/api/bootstrap-static/')
    jsonResponse = r.json()
    with open(file_path, 'w') as outfile:
        json.dump(jsonResponse, outfile)
get_json('fpl.json')
with open('fpl.json') as json_data:
    d = json.load(json_data)
global team_map
team_map ={}
for rec in d["teams"]:
    name = rec["name"]
    i = rec["id"]
    team_map[i]=name
global pos_map
pos_map = {1: "Goalkeeper", 2: "Defender", 3:"Midfielder", 4:"Forward"}
```

# fpl library


```python
async with aiohttp.ClientSession() as session:
    fpl = FPL(session)
    f_players = await fpl.get_players()

    player_table = PrettyTable()
    player_table.field_names = ["Player", "£", "Form", "Games" ,"Mins", "G", "A","Clean Sheets",
                                "Points", "VAPM", "Tr_In","Tr_Out"]
    player_table.align["Player"] = "l"

    for player in f_players:
        if player.web_name == "Coufal":
            print("yes")
            p = player
            break
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-19-2fa093cc8a35> in async-def-wrapper()
          3     f_players = await fpl.get_players()
          4 
    ----> 5     player_table = PrettyTable()
          6     player_table.field_names = ["Player", "£", "Form", "Games" ,"Mins", "G", "A","Clean Sheets",
          7                                 "Points", "VAPM", "Tr_In","Tr_Out"]


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
p.chance_of_playing_next_round
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-8-b30e3d417881> in <module>
    ----> 1 p.chance_of_playing_next_round
          2 


    NameError: name 'p' is not defined



```python
p.selected_by_percent
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-9-08215cbc3dd4> in <module>
    ----> 1 p.selected_by_percent
          2 


    NameError: name 'p' is not defined



```python
def get_gameweek_score(player, gameweek):
    try:
        gameweek_history = [history for history in player.history if history["round"]==gameweek][0]
    except:
        return 0
    return gameweek_history["total_points"]

def get_gameweek_opponent(player, gameweek):
    gameweek_history = next(history for history in player.history
                            if history["round"] == gameweek)
    return (f"{team_converter(gameweek_history['opponent_team'])} ("
            f"{'H' if gameweek_history['was_home'] else 'A'})")

def get_point_difference(player_a, player_b, gameweek):
    if player_a == player_b:
        return 0

    history_a = next(history for history in player_a.history
                    if history["round"] == gameweek)
    history_b = next(history for history in player_b.history
                    if history["round"] == gameweek)

    return history_a["total_points"] - history_b["total_points"]

async def captain_performance(user_id=285714):
    player_table = PrettyTable()
    player_table.field_names = ["Gameweek", "Captain", "Top scorer", "Δ"]
    player_table.align = "r"
    total_difference = 0

    async with aiohttp.ClientSession() as session:
        fpl = FPL(session)
        user = await fpl.get_user(user_id)
        picks = await user.get_picks()
        for i, elements in enumerate(picks):
            elements = picks[elements]
            gameweek = i + 1
            captain_id = next(player for player in elements
                              if player["is_captain"])["element"]
            players = await fpl.get_players(
                [player["element"] for player in elements],
                include_summary=True)

            captain = next(player for player in players
                          if player.id == captain_id)

            top_scorer = max(
                players, key=lambda x: get_gameweek_score(x, gameweek))

            point_difference = get_point_difference(
                captain, top_scorer, gameweek)

            player_table.add_row([
                gameweek,
                (f"{captain.web_name} - "
                f"{get_gameweek_score(captain, gameweek)} points vs. "
                f"{get_gameweek_opponent(captain, gameweek)}"),
                (f"{top_scorer.web_name} - "
                f"{get_gameweek_score(top_scorer, gameweek)} points vs. "
                f"{get_gameweek_opponent(top_scorer, gameweek)}"),
                point_difference
            ])

            total_difference += point_difference

    print(player_table)
    print(f"Total point difference is {abs(total_difference)} points!")
    
async def top_performer(n = 10):
    async with aiohttp.ClientSession() as session:
        fpl = FPL(session)
        players = await fpl.get_players()

    top_performers = sorted(
        players, key=lambda x: x.goals_scored + x.assists, reverse=True)

    player_table = PrettyTable()
    player_table.field_names = ["Player", "£", "G", "A", "G + A"]
    player_table.align["Player"] = "l"

    for player in top_performers[:n]:
        goals = player.goals_scored
        assists = player.assists
        player_table.add_row([player.web_name, f"£{player.now_cost / 10}",
                            goals, assists, goals + assists])

    print(player_table)
    
async def player_performance(players = []):
    async with aiohttp.ClientSession() as session:
        fpl = FPL(session)
        f_players = await fpl.get_players()

        player_table = PrettyTable()
        player_table.field_names = ["Player", "£", "Form", "Games" ,"Mins", "G", "A","Clean Sheets",
                                    "Points", "VAPM", "%Selected","Fitness"]
        player_table.align["Player"] = "l"

        for player in f_players:
            if player.web_name in players:
                goals = player.goals_scored
                assists = player.assists
                if player.chance_of_playing_next_round is None:
                    player.chance_of_playing_next_round = 100
                player_table.add_row([player.web_name, f"£{player.now_cost / 10}",
                                    player.form,await player.games_played,player.minutes,goals,
                                      assists, player.clean_sheets,player.total_points,round(await player.vapm,2)
                                      ,player.selected_by_percent,player.chance_of_playing_next_round])

        print(player_table)

async def fdr_ptable():
    async with aiohttp.ClientSession() as session:
        fpl = FPL(session)
        fdr = await fpl.FDR()

    fdr_table = PrettyTable()
    fdr_table.field_names = [
        "Team", "All (H)", "All (A)", "GK (H)", "GK (A)", "DEF (H)", "DEF (A)",
        "MID (H)", "MID (A)", "FWD (H)", "FWD (A)"]

    for team, positions in fdr.items():
        row = [team]
        for difficulties in positions.values():
            for location in ["H", "A"]:
                if difficulties[location] == 5.0:
                    row.append(Fore.RED + "5.0" + Fore.RESET)
                elif difficulties[location] == 1.0:
                    row.append(Fore.GREEN + "1.0" + Fore.RESET)
                else:
                    row.append(f"{difficulties[location]:.2f}")

        fdr_table.add_row(row)

    fdr_table.align["Team"] = "l"
    print(fdr_table)

    async def get_fdr_diff(team: None):
        global team_map
        async with aiohttp.ClientSession() as session:
            fpl = FPL(session)
            fdr = await fpl.FDR()
            t_detail = await fpl.get_team(team)
            t_is_home = (await t_detail.get_fixtures())[0]["is_home"]
            if t_is_home:
                team2 = (await t_detail.get_fixtures())[0]["team_a"]
                team=team_map[team]
                team2=team_map[team2]
                team = fdr[team]["all"]["H"]
                team2 = fdr[team2]["all"]["A"]
            else:
                team2 = (await t_detail.get_fixtures())[0]["team_h"]
                team=team_map[team]
                team2=team_map[team2]
                team = fdr[team]["all"]["A"]
                team2 = fdr[team2]["all"]["H"]
            return round(team2-team,2)
        
async def next_fixture_difficulty(teams = None,gws=None, zone="all", scaled=False):
    
    global team_map
    ndf = pd.DataFrame(index = teams)
    async with aiohttp.ClientSession() as session:
        fpl = FPL(session)
        fdr = await fpl.FDR()
        for team in team_map.keys():
            team_name=team_map[team]
            if teams is not None and team_name not in teams:
                continue
            t_detail = await fpl.get_team(team)
            fixtures = await t_detail.get_fixtures()
            
            
            if gws is not None:
                fixtures = fixtures[:gws]

            for fix in fixtures:
                try:
                    gameweek = int(fix["event_name"][-2:])
                    t_is_home = fix["is_home"]
                    if t_is_home:
                        team2 = fix["team_a"]
                        team2_name=team_map[team2]
                        
                        if zone == "defence":
                            team_fdr = (fdr[team_name]["goalkeeper"]["H"]+fdr[team_name]["defender"]["H"])/2
                            team2_fdr = (fdr[team2_name]["midfielder"]["A"]+fdr[team2_name]["forward"]["A"])/2
                        elif zone == "attack":
                            team_fdr = (fdr[team_name]["midfielder"]["H"]+fdr[team_name]["forward"]["H"])/2
                            team2_fdr = (fdr[team2_name]["goalkeeper"]["A"]+fdr[team2_name]["defender"]["A"])/2
                        else:
                            team_fdr = fdr[team_name]["all"]["H"]
                            team2_fdr = fdr[team2_name]["all"]["A"]
                    else:
                        team2 = fix["team_h"]
                        team2_name=team_map[team2]
                        if zone == "defence":
                            team_fdr = (fdr[team_name]["goalkeeper"]["A"]+fdr[team_name]["defender"]["A"])/2
                            team2_fdr = (fdr[team2_name]["midfielder"]["H"]+fdr[team2_name]["forward"]["H"])/2
                        elif zone == "attack":
                            team_fdr = (fdr[team_name]["midfielder"]["A"]+fdr[team_name]["forward"]["A"])/2
                            team2_fdr = (fdr[team2_name]["goalkeeper"]["H"]+fdr[team2_name]["defender"]["H"])/2
                        else:
                            team_fdr = fdr[team_name]["all"]["A"]
                            team2_fdr = fdr[team2_name]["all"]["H"]
                    score = (team_fdr - team2_fdr)
                    try:
                        if ndf.at[team_name, gameweek] >= 0:
                            print(team_name,",",team2_name,",",gameweek,score)
                            #ndf.at[team_name, gameweek] = score
                            pass
                    except:
                        pass
                        
                    ndf.at[team_name,gameweek] = (5-round(score,2))
                except:
                    pass
    if scaled and teams is None:
        ndf = ndf.reindex(sorted(ndf.columns), axis=1)
        for idx, col in enumerate(ndf.columns):
            try:
                values = ndf[col].values
                scaled = MinMaxScaler(feature_range=(0,5)).fit_transform(values.reshape(-1, 1))
                scaled = [round(x[0],2) for x in scaled]
                ndf[col] = scaled
            except:
                print(col)
    col = "Average"
    ndf[col] = ndf.mean(axis=1)
    
    
    return ndf.fillna("-")

async def get_vapm_all_players(top=None, pos=list(pos_map.values())):
    async with aiohttp.ClientSession() as session:
        fpl = FPL(session)
        players = await fpl.get_players()
        team = await fpl.get_teams()
        global team_map
        global pos_map
        pos_codes = [k for k,v in pos_map.items() if v in pos]
    
        top_performers = sorted(
            players, key=lambda x: ((float(x.points_per_game)-2.0)/x.now_cost/10), reverse=True)

        player_table = PrettyTable()
        player_table.field_names = ["Player", "Team", "Pos", "Form", "£", "G", "A", "PPM", "VAPM"]
        player_table.align["Player"] = "l"

        for player in top_performers:
            if player.element_type in pos_codes:
                goals = player.goals_scored
                assists = player.assists
                ppm = round(player.total_points/(player.now_cost),2)
        
                player_table.add_row([player.web_name,team_map[player.team],pos_map[player.element_type],player.form,f"£{player.now_cost/10}",
                            goals, assists, ppm, round(await player.vapm,2)])
        if top is None:
            print(player_table)
        else:
            print(player_table.get_string(start=0, end=top))
            
async def get_best_vapm_players(teams= None, top=None, pos=list(pos_map.values())):
    if teams is None:
        await get_vapm_all_players(top,pos)
        return
    global team_map
    global pos_map
    pos_codes = [k for k,v in pos_map.items() if v in pos]
    team_codes = []
    for team in teams: 
        try:
            team_code = [code for code,name in team_map.items() if name==team][0]
            team_codes.append(team_code)
        except:
            print(team," team not found!")
            return
    async with aiohttp.ClientSession() as session:
        fpl = FPL(session)
        players = await fpl.get_players()
        team = await fpl.get_teams()
    
        top_performers = sorted(players, key=lambda x: ((float(x.points_per_game)-2.0)/(x.now_cost/10)), reverse=True)
        player_table = PrettyTable()
        player_table.field_names = ["Player", "Team", "Pos", "Form", "£", "G", "A", "PPM", "VAPM"]
        player_table.align["Player"] = "l"
        
        i = 0
        for player in top_performers:
            if player.chance_of_playing_this_round is None:
                continue
            if (player.team in team_codes) and (player.chance_of_playing_this_round >=75) and (player.element_type in pos_codes):
                goals = player.goals_scored
                assists = player.assists
                ppm = round(player.total_points/(player.now_cost),2)
        
                player_table.add_row([player.web_name,team_map[player.team],pos_map[player.element_type],player.form,f"£{player.now_cost/10}", goals, assists, ppm, round(await player.vapm,2)])
                i+=1
            if i>=top:
                break
            
        print(player_table)
            
async def my_team(user_id = 285714, summary=False):
    async with aiohttp.ClientSession() as session:
        fpl = FPL(session)
        await fpl.login(*creds)
        user = await fpl.get_user(user_id)
        
        transfers_in = await fpl.get_players(
                [player["element_in"] for player in (await user.get_latest_transfers())],
                include_summary=True)
        
        transfers_out = await fpl.get_players(
            [player["element_out"] for player in (await user.get_latest_transfers())],
            include_summary=True)
        
        
        curr_team = await fpl.get_players(
                [player["element"] for player in (await user.get_team())],
                include_summary=True)
        if not summary:
            print("\nTransfers In:")
            for player in transfers_in:
                print(player.web_name)
            print("\nTransfers Out:")
            for player in transfers_out:
                print(player.web_name)
            print("\nCurrent Squad:")
            for player in curr_team:
                print(player.web_name)
        else:
            fields = ["Player", "Team", "Pos", "Minutes", "Form", "VAPM" ,"Injury"]
            
            n_fdr = await next_fixture_difficulty()
            fields = fields + list(n_fdr.columns)
            summary_df = pd.DataFrame(columns=fields)
            for player in curr_team:
                team = team_map[player.team]
                injury = player.chance_of_playing_next_round
                if injury is None:
                    injury = 100
                summary_df.loc[len(summary_df)]=[player.web_name,team_map[player.team],pos_map[player.element_type],player.minutes,player.form,round(await player.vapm,4),injury, *n_fdr.loc[team].values]
                
            return summary_df
        
async def team_ep_this(user_id = 285714):
    global pos_map
    async with aiohttp.ClientSession() as session:
        fpl = FPL(session)
        await fpl.login(*creds)
        user = await fpl.get_user(user_id)
        
        curr_team = await fpl.get_players(
                [player["element"] for player in (await user.get_team())],
                include_summary=True)

        fields = ["Player", "Team", "VAPM", "Pos","Expected Points", "Injury"]

        summary_df = pd.DataFrame(columns=fields)
        for player in curr_team:
            team = team_map[player.team]
            injury = player.chance_of_playing_next_round
            if injury is None:
                injury = 100
            summary_df.loc[len(summary_df)]=[player.web_name,team_map[player.team],round(await player.vapm,4),
                                             pos_map[player.element_type],float(player.ep_this),injury]
#         d = 0.0
#         t = 0.0
#         for name,g in df.groupby(["Pos"]):
#             d+=min(g["Expected Points"].values)
#             t+=sum(g["Expected Points"].values)
        summary_df.drop(["Pos"],axis=1,inplace=True)
        summary_df.loc[len(summary_df)]=["Total","-","-",sum(summary_df["Expected Points"].values),"-"]
        return summary_df
```

# Execution


```python
await captain_performance()
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-21-f446e1606a7d> in async-def-wrapper()
          2 


    <ipython-input-20-d5cb4b00185a> in captain_performance(user_id)
         30 
         31     async with aiohttp.ClientSession() as session:
    ---> 32         fpl = FPL(session)
         33         user = await fpl.get_user(user_id)
         34         picks = await user.get_picks()


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
await fdr_ptable()
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-22-91f80b9dbe78> in async-def-wrapper()
          2 


    <ipython-input-20-d5cb4b00185a> in fdr_ptable()
        112 async def fdr_ptable():
        113     async with aiohttp.ClientSession() as session:
    --> 114         fpl = FPL(session)
        115         fdr = await fpl.FDR()
        116 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
await next_fixture_difficulty(gws=4,zone="all",scaled=True)
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-23-33df67b4d399> in async-def-wrapper()
          2 


    <ipython-input-20-d5cb4b00185a> in next_fixture_difficulty(teams, gws, zone, scaled)
        162     ndf = pd.DataFrame(index = teams)
        163     async with aiohttp.ClientSession() as session:
    --> 164         fpl = FPL(session)
        165         fdr = await fpl.FDR()
        166         for team in team_map.keys():


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
await next_fixture_difficulty(teams=["West Ham","Leeds"], gws=4, zone="attack")
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-24-8f5e49d1e60a> in async-def-wrapper()
          2 


    <ipython-input-20-d5cb4b00185a> in next_fixture_difficulty(teams, gws, zone, scaled)
        162     ndf = pd.DataFrame(index = teams)
        163     async with aiohttp.ClientSession() as session:
    --> 164         fpl = FPL(session)
        165         fdr = await fpl.FDR()
        166         for team in team_map.keys():


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
await get_best_vapm_players(teams=None,top=25,pos=[
#                                                     "Defender",
                                                   "Midfielder",
 #                                                   "Forward"
                                                  ])
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-25-7e44cf583e13> in async-def-wrapper()
          4  #                                                   "Forward"
          5                                                   ])
    ----> 6 
    

    <ipython-input-20-d5cb4b00185a> in get_best_vapm_players(teams, top, pos)
        263 async def get_best_vapm_players(teams= None, top=None, pos=list(pos_map.values())):
        264     if teams is None:
    --> 265         await get_vapm_all_players(top,pos)
        266         return
        267     global team_map


    <ipython-input-20-d5cb4b00185a> in get_vapm_all_players(top, pos)
        234 async def get_vapm_all_players(top=None, pos=list(pos_map.values())):
        235     async with aiohttp.ClientSession() as session:
    --> 236         fpl = FPL(session)
        237         players = await fpl.get_players()
        238         team = await fpl.get_teams()


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
await player_performance(["Rashford"])
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-26-e60c53df80ba> in async-def-wrapper()
          2 


    <ipython-input-20-d5cb4b00185a> in player_performance(players)
         89 async def player_performance(players = []):
         90     async with aiohttp.ClientSession() as session:
    ---> 91         fpl = FPL(session)
         92         f_players = await fpl.get_players()
         93 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
await get_vapm_all_players(top=20)
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-27-559e6b74f116> in async-def-wrapper()
          2 


    <ipython-input-20-d5cb4b00185a> in get_vapm_all_players(top, pos)
        234 async def get_vapm_all_players(top=None, pos=list(pos_map.values())):
        235     async with aiohttp.ClientSession() as session:
    --> 236         fpl = FPL(session)
        237         players = await fpl.get_players()
        238         team = await fpl.get_teams()


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
await my_team(summary=True)
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-28-14ce5c9e205f> in async-def-wrapper()
          2 


    <ipython-input-20-d5cb4b00185a> in my_team(user_id, summary)
        304 async def my_team(user_id = 285714, summary=False):
        305     async with aiohttp.ClientSession() as session:
    --> 306         fpl = FPL(session)
        307         await fpl.login(*creds)
        308         user = await fpl.get_user(user_id)


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
await team_ep_this()
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-29-b608e3e370bb> in async-def-wrapper()
          2 


    <ipython-input-20-d5cb4b00185a> in team_ep_this(user_id)
        348     global pos_map
        349     async with aiohttp.ClientSession() as session:
    --> 350         fpl = FPL(session)
        351         await fpl.login(*creds)
        352         user = await fpl.get_user(user_id)


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
async with aiohttp.ClientSession() as session:
    fpl = FPL(session)
    f_players = await fpl.get_players()
    for player in f_players:
        if player.web_name == "Pulisic":
            break
```


    ---------------------------------------------------------------------------

    SSLCertVerificationError                  Traceback (most recent call last)

    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1316                 h.request(req.get_method(), req.selector, req.data, headers,
    -> 1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in request(self, method, url, body, headers, encode_chunked)
       1228         """Send a complete request to the server."""
    -> 1229         self._send_request(method, url, body, headers, encode_chunked)
       1230 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_request(self, method, url, body, headers, encode_chunked)
       1274             body = _encode(body, 'body')
    -> 1275         self.endheaders(body, encode_chunked=encode_chunked)
       1276 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in endheaders(self, message_body, encode_chunked)
       1223             raise CannotSendHeader()
    -> 1224         self._send_output(message_body, encode_chunked=encode_chunked)
       1225 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in _send_output(self, message_body, encode_chunked)
       1015         del self._buffer[:]
    -> 1016         self.send(msg)
       1017 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in send(self, data)
        955             if self.auto_open:
    --> 956                 self.connect()
        957             else:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/http/client.py in connect(self)
       1391             self.sock = self._context.wrap_socket(self.sock,
    -> 1392                                                   server_hostname=server_hostname)
       1393 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in wrap_socket(self, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, session)
        411             context=self,
    --> 412             session=session
        413         )


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in _create(cls, sock, server_side, do_handshake_on_connect, suppress_ragged_eofs, server_hostname, context, session)
        852                         raise ValueError("do_handshake_on_connect should not be specified for non-blocking sockets")
    --> 853                     self.do_handshake()
        854             except (OSError, ValueError):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/ssl.py in do_handshake(self, block)
       1116                 self.settimeout(None)
    -> 1117             self._sslobj.do_handshake()
       1118         finally:


    SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)

    
    During handling of the above exception, another exception occurred:


    URLError                                  Traceback (most recent call last)

    <ipython-input-30-9de77742feb9> in async-def-wrapper()
          3     f_players = await fpl.get_players()
          4     for player in f_players:
    ----> 5         if player.web_name == "Pulisic":
          6             break
          7 


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/fpl/fpl.py in __init__(self, session)
         48         self.session = session
         49 
    ---> 50         static = json.loads(urlopen(API_URLS["static"]).read().decode("utf-8"))
         51         for k, v in static.items():
         52             try:


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        220     else:
        221         opener = _opener
    --> 222     return opener.open(url, data, timeout)
        223 
        224 def install_opener(opener):


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in open(self, fullurl, data, timeout)
        523             req = meth(req)
        524 
    --> 525         response = self._open(req, data)
        526 
        527         # post-process response


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _open(self, req, data)
        541         protocol = req.type
        542         result = self._call_chain(self.handle_open, protocol, protocol +
    --> 543                                   '_open', req)
        544         if result:
        545             return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        501         for handler in handlers:
        502             func = getattr(handler, meth_name)
    --> 503             result = func(*args)
        504             if result is not None:
        505                 return result


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in https_open(self, req)
       1358         def https_open(self, req):
       1359             return self.do_open(http.client.HTTPSConnection, req,
    -> 1360                 context=self._context, check_hostname=self._check_hostname)
       1361 
       1362         https_request = AbstractHTTPHandler.do_request_


    /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/urllib/request.py in do_open(self, http_class, req, **http_conn_args)
       1317                           encode_chunked=req.has_header('Transfer-encoding'))
       1318             except OSError as err: # timeout error
    -> 1319                 raise URLError(err)
       1320             r = h.getresponse()
       1321         except:


    URLError: <urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1056)>



```python
player.ep_this,player.ep_next
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-31-72643b8f68f7> in <module>
    ----> 1 player.ep_this,player.ep_next
          2 


    NameError: name 'player' is not defined



```python

```
