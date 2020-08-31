#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Created on Sun May 31 18:39:57 2020

@author: brookejackson
"""


import spotipy.oauth2
import spotipy
import spotipy.util as util
import pandas as pd
import json
import sys
import csv
import os


#Read in all historical data from 28 Spotify-curated playlists

os.getcwd()
os.chdir("/Users/brookejackson/Dropbox/Data Science Intensive/Capstone/top_spotify_playlists_history")
os.getcwd()

rapcaviar = pd.read_csv("RapCaviar_Past_Tracks.csv")
peacefulpiano = pd.read_csv("Peaceful Piano_Past_Tracks.csv")
getturnt = pd.read_csv("Get Turnt_Past_Tracks.csv")
hotcountry = pd.read_csv("Hot Country_Past_Tracks.csv")
chillhits = pd.read_csv("Chill Hits_Past_Tracks.csv")
mint = pd.read_csv("mint_Past_Tracks.csv")
happyhits = pd.read_csv("Happy Hits!_Past_Tracks.csv")
moodbooster = pd.read_csv("Mood Booster_Past_Tracks.csv")
arebe = pd.read_csv("Are & Be_Past_Tracks.csv")
motivationmix = pd.read_csv("Motivation Mix_Past_Tracks.csv")
rockthis = pd.read_csv("Rock This_Past_Tracks.csv")
teenparty = pd.read_csv("Teen Party_Past_Tracks.csv")
sleep = pd.read_csv("Sleep_Past_Tracks.csv")
workout = pd.read_csv("Workout_Past_Tracks.csv")
newmusicfriday = pd.read_csv("New Music Friday_Past_Tracks.csv")
powerworkout = pd.read_csv("Power Workout_Past_Tracks.csv")
popremix = pd.read_csv("Pop Remix_Past_Tracks.csv")
cardio = pd.read_csv("Cardio_Past_Tracks.csv")
kpopdaebak = pd.read_csv("K-Pop Daebak_Past_Tracks.csv")
mostnecessary = pd.read_csv("Most Necessary_Past_Tracks.csv")
intensestudy = pd.read_csv("Intense Studying_Past_Tracks.csv")
ultindie = pd.read_csv("Ultimate Indie_Past_Tracks.csv")
hotrhythmic = pd.read_csv("Hot Rhythmic_Past_Tracks.csv")
chilledrb = pd.read_csv("Chilled R&B_Past_Tracks.csv")
dancerising = pd.read_csv("Dance Rising_Past_Tracks.csv")   
popco  = pd.read_csv("PopCo_Past_Tracks.csv")
nextnash = pd.read_csv("Next From Nashville_Past_Tracks.csv")
silksheets = pd.read_csv("Silk Sheets_Past_Tracks.csv")

#
rapcaviar = rapcaviar.assign(playlist_name="rapcaviar")
peacefulpiano = peacefulpiano.assign(playlist_name="peacefulpiano")
getturnt = getturnt.assign(playlist_name="getturnt")
hotcountry  = hotcountry.assign(playlist_name="hotcountry")
chillhits = chillhits.assign(playlist_name="chillhits")
mint = mint.assign(playlist_name="mint")
happyhits = happyhits.assign(playlist_name="happyhits")
moodbooster = moodbooster.assign(playlist_name="moodbooster")
arebe = arebe.assign(playlist_name="arebe")
motivationmix = motivationmix.assign(playlist_name="motivationmix")
rockthis = rockthis.assign(playlist_name="rockthis")
teenparty = teenparty.assign(playlist_name="teenparty")
sleep = sleep.assign(playlist_name="sleep")
workout = workout.assign(playlist_name="workout")
newmusicfriday = newmusicfriday.assign(playlist_name="newmusicfriday")
powerworkout = powerworkout.assign(playlist_name="powerworkout")
popremix = popremix.assign(playlist_name="popremix")
cardio = cardio.assign(playlist_name="cardio")
kpopdaebak = kpopdaebak.assign(playlist_name="kpopdaebak")
mostnecessary = mostnecessary.assign(playlist_name="mostnecessary")
intensestudy = intensestudy.assign(playlist_name="intensestudy")
ultindie = ultindie.assign(playlist_name="ultindie")
hotrhythmic = hotrhythmic.assign(playlist_name="hotrhythmic")
chilledrb = chilledrb.assign(playlist_name="chilledrb")
dancerising = dancerising.assign(playlist_name="dancerising")
popco = popco.assign(playlist_name="popco")
nextnash = nextnash.assign(playlist_name="nextnash")
silksheets = silksheets.assign(playlist_name="silksheets")

#merge all the playlists into  one datafram
data = pd.concat([rapcaviar,peacefulpiano,getturnt,hotcountry,chillhits,mint,happyhits,moodbooster,arebe,motivationmix,rockthis,teenparty,sleep,workout,newmusicfriday,powerworkout,popremix,cardio,kpopdaebak,mostnecessary,intensestudy, ultindie, hotrhythmic,chilledrb, dancerising,popco,nextnash,silksheets])

#create dummy variables for each of the track's playlists that they appear on
data = pd.concat([data, pd.get_dummies(data['playlist_name'])],axis=1);data
data.head()
#export the dataframe with all the songs and playlists to a csv which I will bring into R for further analysis
os.getcwd()
os.chdir("/Users/brookejackson/Dropbox/Data Science Intensive/Capstone")
os.getcwd()
data.to_csv("alltracks.csv")


#Load the Spotipy Package and set personal developer credentials in order to pull features 
#ref: #https://developer.spotify.com/documentation/web-api/reference/tracks/get-several-audio-features/
import spotipy
from spotipy.oauth2 import SpotifyClientCredentials
import time 

client_id = 'f34024903dd84b57bae9be04ff1b2f85'
client_secret = '69e580c8f074494aa1cdc2ad6d3c031b'

client_credentials_manager = SpotifyClientCredentials(client_id, client_secret)
sp = spotipy.Spotify(client_credentials_manager=client_credentials_manager)


#Test: Get the features for a single track:
featuretest = sp.audio_features(['3jnQaDieHcTkkIrkJFIJvC'])
featuretest 
#It works so now we will go on to download all of the playlists' tracks song features for analysis

#Running a loop on the entire dataframe times out Spotify's API so we will 
#run a loop on each playlist to get the features for every song on the playlist

#FIGURE OUT HOW TO DO THIS NEXT PART MORE EFFICIENTLY IN A FUNCTION
#rapcaviar features
#set up an empty list
rapcaviartrackfeatureslist = [] 
#then run the function in a loop across the rapcaviar dataframe
for x in rapcaviar['Spotify Track Ids']:
    rapcaviarfeatures = sp.audio_features([x]) 
    rapcaviartrackfeatureslist.append(rapcaviarfeatures)
#change into a dataframe for export and analysis
rapcaviarfeaturesdf = pd.DataFrame(rapcaviartrackfeatureslist)  
rapcaviarfeaturesdf.to_csv("rapcaviarfeatures.csv")

#peacefulpiano
peacefulpianotrackfeatureslist = [] 
for x in peacefulpiano['Spotify Track Ids']:
    peacefulpianofeatures = sp.audio_features([x]) 
    peacefulpianotrackfeatureslist.append(peacefulpianofeatures)
#change into a dataframe for export and analysis
peacefulpianofeaturesdf = pd.DataFrame(peacefulpianotrackfeatureslist)   
peacefulpianofeaturesdf.to_csv("peacefulpianofeatures.csv") 

#getturnt
getturnttrackfeatureslist = [] 
for x in getturnt['Spotify Track Ids']:
    getturntfeatures = sp.audio_features([x]) 
    getturnttrackfeatureslist.append(peacefulpianofeatures)
#change into a dataframe for export and analysis
getturntfeaturesdf = pd.DataFrame(getturnttrackfeatureslist) 
getturntfeaturesdf.to_csv("getturntfeatures.csv") 

#hotcountry
hotcountrytrackfeatureslist = [] 
for x in hotcountry['Spotify Track Ids']:
    hotcountryfeatures = sp.audio_features([x]) 
    hotcountrytrackfeatureslist.append(hotcountryfeatures)
#change into a dataframe for export and analysis
hotcountryfeaturesdf = pd.DataFrame(hotcountrytrackfeatureslist) 
hotcountryfeaturesdf.to_csv("hotcountryfeatures.csv") 
     
#chillhits 
chillhitstrackfeatureslist = [] 
for x in chillhits['Spotify Track Ids']:
    chillhitsfeatures = sp.audio_features([x]) 
    chillhitstrackfeatureslist.append(chillhitsfeatures)
#change into a dataframe for export and analysis
chillhitsfeaturesdf = pd.DataFrame(chillhitstrackfeatureslist) 
chillhitsfeaturesdf.to_csv("chillhitsfeatures.csv") 

#mint
minttrackfeatureslist = [] 
for x in mint['Spotify Track Ids']:
    mintfeatures = sp.audio_features([x]) 
    minttrackfeatureslist.append(mintfeatures)
#change into a dataframe for export and analysis
mintfeaturesdf = pd.DataFrame(minttrackfeatureslist) 
mintfeaturesdf.to_csv("mintfeatures.csv") 

# happyhits 
happyhitstrackfeatureslist = [] 
for x in happyhits['Spotify Track Ids']:
    happyhitsfeatures = sp.audio_features([x]) 
    happyhitstrackfeatureslist.append(happyhitsfeatures)
#change into a dataframe for export and analysis
happyhitsfeaturesdf = pd.DataFrame(happyhitstrackfeatureslist) 
happyhitsfeaturesdf.to_csv("happyhitsfeatures.csv") 

# moodbooster 
moodboostertrackfeatureslist = [] 
for x in moodbooster['Spotify Track Ids']:
    moodboosterfeatures = sp.audio_features([x]) 
    moodboostertrackfeatureslist.append(moodboosterfeatures)
#change into a dataframe for export and analysis
moodboosterfeaturesdf = pd.DataFrame(moodboostertrackfeatureslist) 
moodboosterfeaturesdf.to_csv("moodboosterfeatures.csv")

# arebe
arebetrackfeatureslist = [] 
for x in arebe['Spotify Track Ids']:
    arebefeatures = sp.audio_features([x]) 
    arebetrackfeatureslist.append(arebefeatures)
#change into a dataframe for export and analysis
arebefeaturesdf = pd.DataFrame(arebetrackfeatureslist) 
arebefeaturesdf.to_csv("arebefeatures.csv")

# motivationmix 
motivationmixtrackfeatureslist = [] 
for x in motivationmix['Spotify Track Ids']:
    motivationmixfeatures = sp.audio_features([x]) 
    motivationmixtrackfeatureslist.append(motivationmixfeatures)
#change into a dataframe for export and analysis
motivationmixfeaturesdf = pd.DataFrame(motivationmixtrackfeatureslist)  
motivationmixfeaturesdf.to_csv("motivationmixfeatures.csv")

# rockthis
rockthistrackfeatureslist = [] 
for x in rockthis['Spotify Track Ids']:
    rockthisfeatures = sp.audio_features([x]) 
    rockthistrackfeatureslist.append(rockthisfeatures)
#change into a dataframe for export and analysis
rockthisfeaturesdf = pd.DataFrame(rockthistrackfeatureslist)  
rockthisfeaturesdf.to_csv("rockthisfeatures.csv")

# teenparty 
teenpartytrackfeatureslist = [] 
for x in teenparty['Spotify Track Ids']:
    teenpartyfeatures = sp.audio_features([x]) 
    teenpartytrackfeatureslist.append(teenpartyfeatures)
#change into a dataframe for export and analysis
teenpartyfeaturesdf = pd.DataFrame(teenpartytrackfeatureslist) 
teenpartyfeaturesdf.to_csv("teenpartyfeatures.csv") 

# sleep 
sleeptrackfeatureslist = [] 
for x in sleep['Spotify Track Ids']:
    sleepfeatures = sp.audio_features([x]) 
    sleeptrackfeatureslist.append(sleepfeatures)
#change into a dataframe for export and analysis
sleepfeaturesdf = pd.DataFrame(sleeptrackfeatureslist)
sleepfeaturesdf.to_csv("sleepfeatures.csv")  

# workout 
workouttrackfeatureslist = [] 
for x in workout['Spotify Track Ids']:
    workoutfeatures = sp.audio_features([x]) 
    workouttrackfeatureslist.append(workoutfeatures)
#change into a dataframe for export and analysis
workoutfeaturesdf = pd.DataFrame(workouttrackfeatureslist)  
workoutfeaturesdf.to_csv("workoutfeatures.csv")

# newmusicfriday 
newmusicfridaytrackfeatureslist = [] 
for x in newmusicfriday['Spotify Track Ids']:
    newmusicfridayfeatures = sp.audio_features([x]) 
    newmusicfridaytrackfeatureslist.append(newmusicfridayfeatures)
#change into a dataframe for export and analysis
newmusicfridayfeaturesdf = pd.DataFrame(newmusicfridaytrackfeatureslist) 
newmusicfridayfeaturesdf.to_csv("newmusicfridayfeatures.csv") 

# powerworkout 
powerworkouttrackfeatureslist = [] 
for x in powerworkout['Spotify Track Ids']:
    powerworkoutfeatures = sp.audio_features([x])
    powerworkouttrackfeatureslist.append(powerworkoutfeatures)
#change into a dataframe for export and analysis
powerworkoutfeaturesdf = pd.DataFrame(powerworkouttrackfeatureslist) 
powerworkoutfeaturesdf.to_csv("powerworkoutfeatures.csv") 

# popremix 
popremixtrackfeatureslist = [] 
for x in popremix['Spotify Track Ids']:
    popremixfeatures = sp.audio_features([x]) 
    popremixtrackfeatureslist.append(popremixfeatures)
#change into a dataframe for export and analysis
popremixfeaturesdf = pd.DataFrame(popremixtrackfeatureslist) 
popremixfeaturesdf.to_csv("popremixfeatures.csv") 

# cardio 
cardiotrackfeatureslist = [] 
for x in cardio['Spotify Track Ids']:
    cardiofeatures = sp.audio_features([x]) 
    cardiotrackfeatureslist.append(cardiofeatures)
#change into a dataframe for export and analysis
cardiofeaturesdf = pd.DataFrame(cardiotrackfeatureslist) 
cardiofeaturesdf.to_csv("cardiofeatures.csv") 

# kpopdaebak 
kpopdaebaktrackfeatureslist = [] 
for x in kpopdaebak['Spotify Track Ids']:
    kpopdaebakfeatures = sp.audio_features([x])
    kpopdaebaktrackfeatureslist.append(kpopdaebakfeatures)
#change into a dataframe for export and analysis
kpopdaebakfeaturesdf = pd.DataFrame(kpopdaebaktrackfeatureslist) 
kpopdaebakfeaturesdf.to_csv("kpopdaebakfeatures.csv") 

# mostnecessary 
mostnecessarytrackfeatureslist = [] 
for x in mostnecessary['Spotify Track Ids']:
    mostnecessaryfeatures = sp.audio_features([x]) 
    mostnecessarytrackfeatureslist.append(mostnecessaryfeatures)
#change into a dataframe for export and analysis
mostnecessaryfeaturesdf = pd.DataFrame(mostnecessarytrackfeatureslist) 
mostnecessaryfeaturesdf.to_csv("mostnecessaryfeatures.csv") 

# intensestudy 
intensestudytrackfeatureslist = [] 
for x in intensestudy ['Spotify Track Ids']:
    intensestudyfeatures = sp.audio_features([x]) 
    intensestudytrackfeatureslist.append(intensestudyfeatures)
#change into a dataframe for export and analysis
intensestudyfeaturesdf = pd.DataFrame(intensestudytrackfeatureslist) 
intensestudyfeaturesdf.to_csv("intensestudyfeatures.csv") 

# ultindie 
ultindietrackfeatureslist = [] 
for x in ultindie['Spotify Track Ids']:
    ultindiefeatures = sp.audio_features([x]) 
    ultindietrackfeatureslist.append(ultindiefeatures)
#change into a dataframe for export and analysis
ultindiefeaturesdf = pd.DataFrame(ultindietrackfeatureslist) 
ultindiefeaturesdf.to_csv("ultindiefeatures.csv") 

# hotrhythmic 
hotrhythmictrackfeatureslist = [] 
for x in hotrhythmic['Spotify Track Ids']:
    hotrhythmicfeatures = sp.audio_features([x]) 
    hotrhythmictrackfeatureslist.append(hotrhythmicfeatures)
#change into a dataframe for export and analysis
hotrhythmicfeaturesdf = pd.DataFrame(hotrhythmictrackfeatureslist) 
hotrhythmicfeaturesdf.to_csv("hotrhythmicfeatures.csv") 

# chilledrb 
chilledrbtrackfeatureslist = [] 
for x in chilledrb['Spotify Track Ids']:
    chilledrbfeatures = sp.audio_features([x]) 
    chilledrbtrackfeatureslist.append(chilledrbfeatures)
#change into a dataframe for export and analysis
chilledrbfeaturesdf = pd.DataFrame(chilledrbtrackfeatureslist) 
chilledrbfeaturesdf.to_csv("chilledrbfeatures.csv") 

# dancerising
dancerisingtrackfeatureslist = [] 
for x in dancerising['Spotify Track Ids']:
    dancerisingfeatures = sp.audio_features([x]) 
    dancerisingtrackfeatureslist.append(dancerisingfeatures)
#change into a dataframe for export and analysis
dancerisingfeaturesdf = pd.DataFrame(dancerisingtrackfeatureslist) 
dancerisingfeaturesdf.to_csv("dancerisingfeatures.csv")

# popco 
popcotrackfeatureslist = [] 
for x in popco['Spotify Track Ids']:
    popcofeatures = sp.audio_features([x]) 
    popcotrackfeatureslist.append(popcofeatures)
#change into a dataframe for export and analysis
popcofeaturesdf = pd.DataFrame(popcotrackfeatureslist) 
popcofeaturesdf.to_csv("popcofeatures.csv") 

# nextnash 
nextnashtrackfeatureslist = [] 
for x in nextnash['Spotify Track Ids']:
    nextnashfeatures = sp.audio_features([x]) 
    nextnashtrackfeatureslist.append(nextnashfeatures)
#change into a dataframe for export and analysis
nextnashfeaturesdf = pd.DataFrame(nextnashtrackfeatureslist) 
nextnashfeaturesdf.to_csv("nextnashfeatures.csv") 

# silksheets
silksheetstrackfeatureslist = [] 
for x in silksheets['Spotify Track Ids']:
    silksheetsfeatures = sp.audio_features([x]) 
    silksheetstrackfeatureslist.append(silksheetsfeatures)
#change into a dataframe for export and analysis
silksheetsfeaturesdf = pd.DataFrame(silksheetstrackfeatureslist) 
silksheetsfeaturesdf.to_csv("silksheetsfeatures.csv") 
    


####Now for the testing set, read in all the data from the current week's tracks (May 29, 2020)
os.getcwd()
os.chdir("/Users/brookejackson/Dropbox/Data Science Intensive/Capstone/playlists 052920")
os.getcwd()
rapcaviarc = pd.read_csv("RapCaviar_Current_Tracks.csv")
peacefulpianoc = pd.read_csv("Peaceful Piano_Current_Tracks.csv")
getturntc = pd.read_csv("Get Turnt_Current_Tracks.csv")
hotcountryc = pd.read_csv("Hot Country_Current_Tracks.csv")
chillhitsc = pd.read_csv("Chill Hits_Current_Tracks.csv")
mintc = pd.read_csv("mint_Current_Tracks.csv")
happyhitsc = pd.read_csv("Happy Hits!_Current_Tracks.csv")
moodboosterc = pd.read_csv("Mood Booster_Current_Tracks.csv")
arebec = pd.read_csv("Are & Be_Current_Tracks.csv")
motivationmixc = pd.read_csv("Motivation Mix_Current_Tracks.csv")
rockthisc = pd.read_csv("Rock This_Current_Tracks.csv")
teenpartyc = pd.read_csv("Teen Party_Current_Tracks.csv")
sleepc = pd.read_csv("Sleep_Current_Tracks.csv")
workoutc = pd.read_csv("Workout_Current_Tracks.csv")
newmusicfridayc = pd.read_csv("New Music Friday_Current_Tracks.csv")
powerworkoutc = pd.read_csv("Power Workout_Current_Tracks.csv")
popremixc = pd.read_csv("Pop Remix_Current_Tracks.csv")
cardioc = pd.read_csv("Cardio_Current_Tracks.csv")
kpopdaebakc = pd.read_csv("K-Pop Daebak_Current_Tracks.csv")
mostnecessaryc = pd.read_csv("Most Necessary_Current_Tracks.csv")
intensestudyc = pd.read_csv("Intense Studying_Current_Tracks.csv")
ultindiec = pd.read_csv("Ultimate Indie_Current_Tracks.csv")
hotrhythmicc = pd.read_csv("Hot Rhythmic_Current_Tracks.csv")
chilledrbc = pd.read_csv("Chilled R&B_Current_Tracks.csv")
dancerisingc = pd.read_csv("Dance Rising_Current_Tracks.csv")   
popcoc  = pd.read_csv("PopCo_Current_Tracks.csv")
nextnashc = pd.read_csv("Next From Nashville_Current_Tracks.csv")
silksheetsc = pd.read_csv("Silk Sheets_Current_Tracks.csv")

rapcaviarc = rapcaviarc.assign(playlist_name="rapcaviar")
peacefulpianoc = peacefulpianoc.assign(playlist_name="peacefulpiano")
getturntc = getturntc.assign(playlist_name="getturnt")
hotcountryc  = hotcountryc.assign(playlist_name="hotcountry")
chillhitsc = chillhitsc.assign(playlist_name="chillhits")
mintc = mintc.assign(playlist_name="mint")
happyhitsc = happyhitsc.assign(playlist_name="happyhits")
moodboosterc = moodboosterc.assign(playlist_name="moodbooster")
arebec = arebec.assign(playlist_name="arebe")
motivationmixc = motivationmixc.assign(playlist_name="motivationmix")
rockthisc = rockthisc.assign(playlist_name="rockthis")
teenpartyc = teenpartyc.assign(playlist_name="teenparty")
sleepc = sleepc.assign(playlist_name="sleep")
workoutc = workoutc.assign(playlist_name="workout")
newmusicfridayc = newmusicfridayc.assign(playlist_name="newmusicfriday")
powerworkoutc = powerworkoutc.assign(playlist_name="powerworkout")
popremixc = popremixc.assign(playlist_name="popremix")
cardioc = cardioc.assign(playlist_name="cardio")
kpopdaebakc = kpopdaebakc.assign(playlist_name="kpopdaebak")
mostnecessaryc = mostnecessaryc.assign(playlist_name="mostnecessary")
intensestudyc = intensestudyc.assign(playlist_name="intensestudy")
ultindiec = ultindiec.assign(playlist_name="ultindie")
hotrhythmicc = hotrhythmicc.assign(playlist_name="hotrhythmic")
chilledrbc = chilledrbc.assign(playlist_name="chilledrb")
dancerisingc = dancerisingc.assign(playlist_name="dancerising")
popcoc = popcoc.assign(playlist_name="popco")
nextnashc = nextnashc.assign(playlist_name="nextnash")
silksheetsc = silksheetsc.assign(playlist_name="silksheets")

currenttracks = pd.concat([rapcaviarc,peacefulpianoc,getturntc,hotcountryc,chillhitsc,mintc,happyhitsc,moodboosterc,arebec,motivationmixc,rockthisc,teenpartyc,sleepc,workoutc,newmusicfridayc,powerworkoutc,popremixc,cardioc,kpopdaebakc,mostnecessaryc,intensestudyc, ultindiec, hotrhythmicc,chilledrbc, dancerisingc,popcoc,nextnashc,silksheetsc])

currenttracks = pd.concat([currenttracks, pd.get_dummies(currenttracks['playlist_name'])],axis=1);currenttracks
currenttracks.head()

os.getcwd()
os.chdir("/Users/brookejackson/Dropbox/Data Science Intensive/Capstone")
os.getcwd()
currenttracks.to_csv("tracks_052920.csv")

#get the audio features for the current songs
currenttrackfeatureslist = [] 
for x in currenttracks['Spotify Track Ids']:
    currenttrackfeatures = sp.audio_features([x]) 
    currenttrackfeatureslist.append(currenttrackfeatures)
#change into a dataframe for export and analysis
currenttrackfeaturesdf = pd.DataFrame(currenttrackfeatureslist) 

currenttrackfeaturesdf.to_csv("currenttracksfeatures.csv") 
    


