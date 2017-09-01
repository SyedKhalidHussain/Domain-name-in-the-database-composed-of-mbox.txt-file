# Domain-name-in-the-database-composed-of-mbox.txt-file
# Database access using Python

# Count the domain name in the mbox.txt store in the database
import urllib.request
import sqlite3import re

conn = sqlite3.connect('Emaildb.sqlite')
cur = conn.cursor()
cur.execute(''' CREATE TABLE IF NOT EXISTS Counts (org TEXT, count INTEGER)''')

fname = 'mbox.txt'
filedata = open(fname)

for line in filedata:    
  if not line.startswith('From'):continue    
  
  pieces = line.split()    
  
  if len(pieces)<3:continue    
  
  words = pieces[1]    
  domain = words[words.find('@')+1:]    
  #print(domain)    
  
  cur.execute('SELECT count FROM Counts WHERE org = ?',(domain,))    
  row = cur.fetchone()    
  
  if row is None:        
    cur.execute(''' INSERT INTO Counts (org,count) VALUES (?,1) ''',(domain,))    
  else:        
    cur.execute(' UPDATE Counts SET count = count + 1 WHERE org = ? ',(domain,))        
  
  conn.commit()cur.close()
