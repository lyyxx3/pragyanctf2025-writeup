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
<br>
From this output, we can see a few files that seem sus. amogus
<br>
`0xa73e9cf0      \Users\sai\Desktop\imp.txt ` <br>
`0xa73ea3b0      \Users\sai\Desktop\ks.py ` <br>
`0xa79e8af0      \Users\sai\Desktop\script.py` <br>
`0xb7e5f920      \Users\sai\Desktop\protected.zip` 
<br>
<br>
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
<br>
Let's find the actual value of `key` first, according to the value of `nottheactualkey = "FindMeInTheEnvironment" `, we need to find the `key` in the environment variables.
<br>
<br>
To get the environment variable, we can use this command: <br>
`./vol.py -f memdumpfin.mem windows.envars | grep key`
<br>
<br>
![image](https://github.com/user-attachments/assets/1692ca71-3429-4acb-bc79-708fd791ae27)
<br>
We got the key, which is `Env4rs_1s_4m4z1ng` , now moving on to getting the `passkey`.
<br>
<br>
The script says `print("I seem to have sent the 'PASSKEY' over the internet")`, I got a bit stuck at this part so I just decided to strings and grep the memory dump. (brute force =w=)
<br>
Using this command, `strings memdumpfin.mem | grep -i "passkey"` , while it was running, I saw `passkey.com` so I stopped the process and found the passkey value. <br>
![image](https://github.com/user-attachments/assets/67e5b30a-02c3-4873-88bc-37344be1aff7)
<br>
<br>
`PASSKEY="gAAAAABnc46I0KBYbK0bp8FCqJVN--3Ej3k0fOke6MbxKrwFUqg1wvLhY0ks0IcpDJsXHpEbbSLw-gXiK05EW9HGCUYbjYddqArU_cRnlAL8BLW6Noq14HdfZXKDHRaRJ9-p1YPfpXG8nR92Qq-Sv7wSuiJ36kfGxe9DMdi67OvGzNmF3V5KJS1_lezSTlFsCh-DZbNvnT60`
<br>
<br>
We got the values we need to decrypt now, replace the fake key value with the real key value and enter the passkey, then voila, decrypted :D
<br>
![image](https://github.com/user-attachments/assets/4291f24d-c634-4a14-a511-ef1ad9144593)
<br>
<br>
We got a drive link from that, let's check that out.
<br>
<br>
It is a gif file that we can download, from the looks of it, it is morse code.
<br>
![image](https://github.com/user-attachments/assets/7712b404-cb4c-43e6-9982-8777bdd55a91)
<br>
I went to a Split GIF to frames website so I can record down the code easily.
<br>
![image](https://github.com/user-attachments/assets/dbf65f9d-a9fe-4f83-820b-e5450d844cab)
<br>
Then go to a Morse Code Translator website and decode it.
<br>
![image](https://github.com/user-attachments/assets/3d2fefcf-cdf6-46c0-86c9-4094c1156d51)
<br>
Okay :3 this seems to be the password to the `protected.zip`, let's go unzip it using this as the password. This took me a few tries cuz the author's note "Capitalize" could mean a few combinations (capitalize all letters, capitalize the first letter of each word....)
<br>
Unlock the zip file using `Openthisfilesesame` <br>
![image](https://github.com/user-attachments/assets/fcd07d83-8c44-45e2-8708-58d2e13392c6)
<br>
<br>

## **Step 3: Analyzing the Image File**
After unzipping, we get an image file that seemingly doesn't contain anything useful.
<br>
![image](https://github.com/user-attachments/assets/1dc17c36-443c-4ff6-8ee5-abc9fda92a3f)
<br>
<br>
After trying commands like file, exiftool, binwalk, foremost and stegsolve, I didn't get anything useful. Making steghide my final idea to solve this, but we don't have the password. <br>
Remember the sus files that we found earlier when analyzing the memory dump? There were still some files that we haven't analyze yet, so back to volatility we go~~~
<br>
Let's dump out `0xa73e9cf0      \Users\sai\Desktop\imp.txt ` and `0xa73ea3b0      \Users\sai\Desktop\ks.py ` 
<br>
Checking the imp.txt, doesn't seem to contain anything useful.
<br>
![image](https://github.com/user-attachments/assets/f2c895fa-9ed7-446a-b804-54bf095fbb9c)
<br>
<br>

Moving on to `ks.py` then, we found a base64 encoded string :D
<br>
![image](https://github.com/user-attachments/assets/3c739259-61b7-4852-9777-1412d6bc2d05)
<br>
You can go to a website called CyberChef to decode it, or decode it from CLI with the command: `echo UGFzc3dvcmR7WTB1X200eV9wYTU1fQ== | base64 --decode`. We got `Password{Y0u_m4y_pa55}`
<br>
![image](https://github.com/user-attachments/assets/641d5766-1ed6-4ed9-9d38-d695723dff53)
<br>
Now, we can try using this as password to steghide extract information from the image file.
<br>
<br>

## **Step 4: Getting the Flag**
Using the command, `steghide extract -p "Y0u_m4y_pa55" -sf fin.jpg`, we got the flag =w=
<br>
![image](https://github.com/user-attachments/assets/847c93da-4e8f-4e5e-8a65-26b39068e0df)
<br>
<br>
**Flag:** p_ctf{Th3fla4gsureisV0latil3}




















