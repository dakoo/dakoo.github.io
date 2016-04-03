---
layout: post
title: python - multi-table db
description: python
modified: 2016-04-02
tags: [python]
comments: true
image:
  feature: algorithm.jpeg
---
The following example is an example code to an XML file and produce a properly normalized database. 
### Multi-table DB exmple

```python
import xml.etree.ElementTree as ET
import sqlite3

conn = sqlite3.connect('trackdb.sqlite')
cur = conn.cursor()

cur.executescript('''
	DROP TABLE IF EXISTS Artist;
	DROP TABLE IF EXISTS Genre;
	DROP TABLE IF EXISTS Album;
	DROP TABLE IF EXISTS Track;
	''')
cur.execute('''
	CREATE TABLE IF NOT EXISTS Artist (
		id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT UNIQUE,
		name TEXT UNIQUE
	)
	''')
cur.execute('''
	CREATE TABLE IF NOT EXISTS Genre (
		id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT UNIQUE,
		name TEXT UNIQUE
	)
	''') 
cur.execute('''
	CREATE TABLE IF NOT EXISTS Album (
		id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT UNIQUE,
		artist_id INTEGER,
		title TEXT UNIQUE
	)
	''')
cur.execute('''
	CREATE TABLE IF NOT EXISTS Track (
		id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT UNIQUE,
		title TEXT UNIQUE,
		album_id INTEGER,
		genre_id INTEGER, 
		len INTEGER,
		rating INTEGER,
		count INTEGER
	)
	''')

''' Library.xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Major Version</key><integer>1</integer>
    <key>Minor Version</key><integer>1</integer>
    <key>Date</key><date>2015-11-24T11:12:10Z</date>
    <key>Application Version</key><string>12.3.1.23</string>
    <key>Features</key><integer>5</integer>
    <key>Show Content Ratings</key><true/>
    <key>Music Folder</key><string>file:///Users/csev/Music/iTunes/iTunes%20Music/</string>
    <key>Library Persistent ID</key><string>B7006C9E9799282E</string>
    <key>Tracks</key>
    <dict>
        <key>369</key>
        <dict>
            <key>Track ID</key><integer>369</integer>
            <key>Name</key><string>Another One Bites The Dust</string>
            <key>Artist</key><string>Queen</string>
            <key>Composer</key><string>John Deacon</string>
            <key>Album</key><string>Greatest Hits</string>
            <key>Genre</key><string>Rock</string>
            <key>Kind</key><string>MPEG audio file</string>
            <key>Size</key><integer>4344295</integer>
            <key>Total Time</key><integer>217103</integer>
            <key>Disc Number</key><integer>1</integer>
            <key>Disc Count</key><integer>1</integer>
            <key>Track Number</key><integer>3</integer>
            <key>Track Count</key><integer>17</integer>
            <key>Year</key><integer>1980</integer>
            <key>Date Modified</key><date>2006-02-14T16:13:02Z</date>
            <key>Date Added</key><date>2006-02-14T16:12:53Z</date>
            <key>Bit Rate</key><integer>160</integer>
            <key>Sample Rate</key><integer>44100</integer>
            <key>Play Count</key><integer>55</integer>
            <key>Play Date</key><integer>3518868190</integer>
            <key>Play Date UTC</key><date>2015-07-04T19:23:10Z</date>
            <key>Skip Count</key><integer>1</integer>
            <key>Skip Date</key><date>2015-10-14T23:31:47Z</date>
            <key>Rating</key><integer>100</integer>
            <key>Album Rating</key><integer>100</integer>
            <key>Album Rating Computed</key><true/>
            <key>Normalization</key><integer>1511</integer>
            <key>Compilation</key><true/>
            <key>Persistent ID</key><string>21130E105F3B8845</string>
            <key>Track Type</key><string>File</string>
            <key>File Type</key><integer>1297106739</integer>
            <key>Location</key><string>file:///Users/csev/Music/iTunes/iTunes%20Music/Compilations/Greatest%20Hits/03%20Another%20One%20Bites%20The%20Dust.mp3</string>
            <key>File Folder Count</key><integer>4</integer>
            <key>Library Folder Count</key><integer>1</integer>
        </dict>
        <dict>
        ...
        </dict>
    ...
'''
def lookup(p, key):
	found = False
	for c in p:
		if found: return c.text
		if c.tag == 'key' and c.text == key: 
			found = True
	return None

stuff = ET.parse('Library.xml')
all = stuff.findall('dict/dict/dict')
print 'Dict count:', len(all)

for entry in all:
	if lookup(entry, 'Track ID') is None : continue
	artist = lookup(entry, 'Artist')
	genre = lookup(entry, 'Genre')
	album = lookup(entry, 'Album')
	title = lookup(entry, 'Name')
	length = lookup(entry, 'Total Time')
	rating = lookup(entry, 'Rating')
	count = lookup(entry, 'Play Count')
	if title is None or artist is None or album is None or genre is None: continue
	cur.execute('''INSERT OR IGNORE INTO Artist (name) VALUES ( ? )''', ( artist, ) )
	cur.execute('SELECT id FROM Artist WHERE name = ? ', (artist, ))
	artist_id = cur.fetchone()[0]
	cur.execute('''INSERT OR IGNORE INTO Genre (name) VALUES ( ? )''', ( genre, ) )
	cur.execute('SELECT id FROM Genre WHERE name = ? ', (genre, ))
	genre_id = cur.fetchone()[0]
	cur.execute('''INSERT OR IGNORE INTO Album (artist_id, title) VALUES (?, ?)''', (artist_id, album))
	cur.execute('SELECT id FROM Album WHERE title = ? ', (album, ))
	album_id = cur.fetchone()[0]
	cur.execute('''INSERT OR IGNORE INTO Track (title, album_id, genre_id, len, rating, count) VALUES (?, ?, ?, ?, ?, ?)''', (title, album_id, genre_id, length, rating, count))
	cur.execute('SELECT id FROM Track WHERE title = ? ', (title, ))
	track_id = cur.fetchone()[0]

conn.commit()

cur.execute('''
	SELECT Track.title, Artist.name, Album.title, Genre.name FROM Track JOIN Album JOIN Artist JOIN Genre 
	ON Track.genre_id = Genre.ID and Track.album_id = Album.id and Album.artist_id = Artist.id
	ORDER BY Artist.name 
	LIMIT 3
	''')
rows = cur.fetchall()
for row in rows:
	print "Title:", row[0], ",Artist:", row[1] ,",Album:", row[2] ,",Genre:", row[3]
```
