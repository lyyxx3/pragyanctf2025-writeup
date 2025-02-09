# Forensic Challenge: Memoria Obscuria
![image](https://github.com/user-attachments/assets/bcf38a10-0018-4d5f-b1a3-ec88dc1ba29b)
<br>
<br>

## **Step 1: Analyzing the Challenge**
From this challenge, we are given a memory dump file. Reading the description of the challenge, it mentioned `protected archive` and `script`.
<br>
So, I will try to do a file scan using volatility to see if I can find anything useful.
Here is a useful website containing volatility commands cheatsheat, [Volatility CheatSheet](https://book.hacktricks.wiki/en/generic-methodologies-and-resources/basic-forensic-methodology/memory-dump-analysis/volatility-cheatsheet.html)
<br>
<br>

## **Step 2: Analyzing the Memory Dump using Volatility**
Firstly, I did a file scan using the command below:
<br>
`./vol.py -f memdumpfin.mem windows.filescan.FileScan | grep Desktop`
<br>
I did grep Desktop because in most cases, you will find what you need in folders such as Desktop, Documents or Downloads. Ofc you should try grepping other keywords like flag, admin, or file extensions. But if you don't really know what you are looking for, grepping for Desktop is usually a good way to start.
<br>
<br>
![image](https://github.com/user-attachments/assets/5fec2a4b-1521-47e4-97f0-d421ec15bc49)
<br>
From this output, we can see a few files that seem sus. amogus
<br>
`0xa73e9cf0      \Users\sai\Desktop\imp.txt ` <br>
`0xa73ea3b0      \Users\sai\Desktop\ks.py ` <br>
`0xa79e8af0      \Users\sai\Desktop\script.py` <br>
`0xb7e5f920      \Users\sai\Desktop\protected.zip` <br>
We found the `protected archive` and `script` that the description mentioned, let's check those out first. We can now dump out the files using the command below: <br>
`./vol.py -f memdumpfin.mem windows.dumpfiles.DumpFiles --virtaddr <address>` <br>
![image](https://github.com/user-attachments/assets/092629a1-a637-42ae-a047-0f16a2022305)
<br>
It says Error dumping file but it doesn't matter, it still works fine :D
<br>
<br>

## **Step 2: Analyzing the Script.py**
Now, let's take a look at the `script.py` that we dumped out. 
<br>
![image](https://github.com/user-attachments/assets/5a0c6081-b922-4ff5-a172-1f1a8b10dcc6)
<br>
It seems to be a decryption script, we need the find the values of `key` and `PASSKEY` in order to run this script.






