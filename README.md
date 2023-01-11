## Volatility Room - TryHackMe ###

### Q1 - What is the build version of the host machine in Case 001? ###
```2600.xpsp.080413-2111``` run ```python3 vol.py -f /Scenarios/Investigations/Investigation-1.vmem windows.info``` The answer will be in this information.
![Question1](https://user-images.githubusercontent.com/18509521/211746435-9eceb97b-7144-498b-8771-59b91a7d3ba2.png)

### Q2 - At what time was the memory file acquired in Case 001? ###
```2012-07-22 02:45:08``` Same as above, this informaiton is gathered from running windows.info and is located under SystemTime

### Q3 - What process can be considered suspicious in Case 001? ###
```reader_sl.exe``` We can find this by running windows.pslist, windows.pstree or windows.psscan. This process was the last running process on the machine and doesn't match any of the default services you would see.
![Q3](https://user-images.githubusercontent.com/18509521/211747946-a5dbff01-ad7d-495d-a873-fa9167b11d97.png)

### Q4 - What is the parent process of the suspicious process in Case 001? ###
```explorer.exe``` To identify this one we need to run windows.pstree as it will show all parent and child processes. The asterisk shows it is the child process of explorer.exe
![Q4](https://user-images.githubusercontent.com/18509521/211748355-5b01f338-5561-40fe-baf6-aa50bbcaafbd.png)

### Q5 - What is the PID of the suspicious process in Case 001? ###
```1640``` This can be identified by any of the process dump listed before.

### Q6 - What is the parent process PID in Case 001? ###
```1484``` This can be identified by using windows.pstree

### Q7 - What user-agent was employed by the adversary in Case 001? ###
```Mozilla/5.0 (Windows; U; MSIE 7.0; Windows NT 6.0; en-US)``` Run the command ```python3 vol.py -f /Scenarios/Investigations/Investigation-1.vmem -o /tmp/dump windows.memmap --pid 1640 --dump``` This is dumping the suspicious process which will make it searchable to us. After it has been dumped run strings on the process and grep the user agent.

![useragent](https://user-images.githubusercontent.com/18509521/211750739-567803cc-db05-4141-aaec-9e7ae1c76995.png)

### Q8 - Was Chase Bank one of the suspicious bank domains found in Case 001? (Y/N) ###
```Y``` Running the command ```strings pid.1640.dmp | grep -Eo "(http|https)://[a-zA-Z0-9./?=_%:-]*" | sort -u``` will show us all links that are present within the malicious file among many links Chase bank is one of them listed in there.
![chase](https://user-images.githubusercontent.com/18509521/211751566-d02b289a-834c-4698-a677-ca26f57ef9a4.png)

### Q9 - What suspicious process is running at PID 740 in Case 002? ###
```@WanaDecryptor@``` Run windows.pstree to gather this information
![wanadecrypt](https://user-images.githubusercontent.com/18509521/211752212-c12bcbcd-b0c5-43a6-95e1-b2f027b85bba.png)

### Q10 - What is the full path of the suspicious binary in PID 740 in Case 002? ###
```C:\Intel\ivecuqmanpnirkt615\@WanaDecryptor@.exe``` For this question I ran windows.filescan and dumped it into a file then used grep to search for the process, however there is another way which I will show below.
![wanaFullPath](https://user-images.githubusercontent.com/18509521/211753742-559c1d9f-ebc8-4b6f-abc1-0f1626f993b4.png)

The second way is to run windows.dlllist and grep for the process ID within that output.
![wannaFullPath2](https://user-images.githubusercontent.com/18509521/211753860-5bd58f09-ce95-4593-8486-f62e0bcd7736.png)

### Q11 - What is the parent process of PID 740 in Case 002? ###
```tasksche.exe``` This can be found using windows.pstree and seeing what process is above it (look at the astericks)
![tasksche](https://user-images.githubusercontent.com/18509521/211754258-46cb31ca-4f45-445c-8188-c2efbf633da9.png)

### Q12 - What is the suspicious parent process PID connected to the decryptor in Case 002? ###
```1940``` Same as above

### Q13 - From our current information, what malware is present on the system in Case 002? ###
```wannacry``` Pretty self explantory

### Q14 - What DLL is loaded by the decryptor used for socket creation in Case 002? ###
```ws2_32.dll``` All DLL's can be identified from the miage below or running windows.dlllist and grepping for the malicious files pid. From there we can have a look at the dll's and see if any stick out. In my case no so I googled "Windows dll socket creation" and found my answer there.
![dll](https://user-images.githubusercontent.com/18509521/211755949-5a3ae4c8-dc8f-4946-a5f3-7b069efda610.png)

### Q15 - What mutex can be found that is a known indicator of the malware in question in Case 002? ###
```MsWinZonesCacheCounterMutexA``` For this one as we have idntified that the malware is most likely wanncry I looked up what wannacry's mutex handles were and searched them in windows.handles utiliing grep.
![mutex](https://user-images.githubusercontent.com/18509521/211758652-30a646ce-b12b-4cb6-af4d-54f9b7ac1ceb.png)

### Q16 - What plugin could be used to identify all files loaded from the malware working directory in Case 002? ###
```windows.filescan```

