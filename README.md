# Lemmatizer Server
## AWS Lightsail Setup
1. Navigate to the Lightsail home page.
2. Click on the “Create Instance” button.
3. Pick the closest region to you as the location.
4. Choose linux/unix as your platform.
5. Under the “Select a blueprint” section, select the OS Only button and then select the latest Ubuntu version beneath.
6. Scroll down to “Enable Automatic Snapshots” and set a time for the server to automatically back up.
7. Under the “Choose your instance plan” section, choose the cheapest instance which gives you one month or 750 hours free.
8. Under “Identify your instance”, choose the name of the instance
9. Click “Create Instance”.
10. Wait for the instance to get up and running.
11. Click on the instance to access the information within.
12. Check the status in the top right corner below the Navigation bar and make sure it is “Running”. If it is “Stopped”, select the “Start” button.
13. Find the navigation bar dropdown next to AWS and select “Account”.
14. Move to the tab called “SSH keys” and then download the ssh key for the server location.
15. Go back to home and select the server you created again.
16. Navigate to the “Network” tab and click on the option to create a Static IP. This creates an IP that can be consistently connected to through ssh.
17. Move down to the IPv4 firewall and select “Add rule”.
18. For “Application”, select custom, for “Protocol”, select TCP, and for “Port or range”, type 9050.
19. Click the create button to create the rule.
20. Download FileZilla for windows from this link.
21. Once you have downloaded and opened FileZilla, navigate to where the server file(s) you would like to upload are in the left pane.
22. Navigate to File>Site Manager and select the “New Site” option under the left panel of the popup.
23. For the Protocol, select “SFTP -SSH File Transfer Protocol”.
24. Type out the static IP address you created next to “Host:”.
25. As “Logon Type:”, select “Key file”. Then type ubuntu as the “User” and for the “Key file”, browse to the location you downloaded the ssh key to.
26. Press “Connect” and files should appear in the right pane of FileZilla.
27. Now upload the necessary server files onto the server.
28. Switch back to the AWS Lightsail window.
29. Navigate to the “Connect” tab and select “Connect using SSH”
30. To start running the server type “python3 [server_filename.py]”
31. To run the server constantly even without a terminal window open, type “screen python3 [server_filename.py]”
32. To cancel a server that you have used the screen command on, type “htop”, navigate to the server operation, and cancel the operation.

## Python Server Structure  
<ins>Folder</ins>  
Server Ops (Has an __init__.py file so it can be used as a package)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server_main.py  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**switch_function(sentence_json)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Checks to see if the function of the passed in json is one of the function options and runs the appropriate function and returns the json if so, otherwise returns the json passed in.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**run_wordnet(sentence_json)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Runs the appropriate lemmatizer and returns the lemmatized json  

Lemmatizers (Has an __init__.py file so it can be used as a package)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server_wordnet_lemma.py  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**get_wordnet_pos(word)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Gets the part of speech of a word if it can be found, otherwise returns the tag as a noun  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**lemmatize(sentence_json)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Runs the wordnet lemmatizer on the text of the json and sets the lemmas field of the json to be the array of lemmas returned by the lemmatizer  

Main  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;PythonMultiServer.py  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**clientThread(sock)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Gets the json packet from the socket, runs the desired function on the json packet, and returns the packet to the user.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**main()**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Binds the socket to the correct port from the server, accepts the client connection and gets the client and address, and creates a new thread for the client to run the necessary function  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Downloader.py  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Upload and run this file to download whatever specific pieces of the lemmatizer are necessary  

## Python Client Structure  
<ins>File</ins>  
Client Namespace (Client.cs)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Access  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Lemmatize(string fileName, Lemmatizer lemmaMaker)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Reads the text from the specified file, converts the lemmatizer object to a JSON, sends it to the server, gets back string and returns the string converted back from a JSON to a Lemamtizer object  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ConnectAndExecute(string message)**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Connects to the server, throws exception if it cannot connect, otherwise sends the input message and returns the received string  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ReadFile(string filename)**  
Reads all the text from the specified file and returns it as a string  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Lemmatizer  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;The lemmatizer object that holds the function for the server to run, the text for the server to send, and the lemma array for the server to return  

LemmatizerClient.cs  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Run()**  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Creates a lemmatizer object and initializes it to the wordnet function then runs the server code to lemmatize the words and outputs one of the lemmas to the screen  

## Notes for Unity project:  
* This source stated that the Hololens can only use TCP ports 9000-9100. Most other sources seemed to state that Hololens could use any port.  
* In the appxmanifest for the built Unity project, go to Capabilities and enable the Private Networks (Client and Server), Internet (Client and Server), and Internet (Client) to allow the Hololens to correctly access the server.  

## Notes for Lemmatizer Folder
lemmatize_text.txt: Holds the biomedical text  
lemmatize_text_I.txt: Holds the conversational text  
  
**Lemmatization Scripts:**  
* For LuiNorm, the Lemmatizer needs to be downloaded from its site.
* original_generator.py: Generates the csv for the original words to be compared against the lemmas
* geniatagger: Holds the GENIA wrapper to make it easier to use with the OS and the actual gennia lemmatizer (you will need to delete the geniatagger-3.0.2 folder and reinstall genia using the geniatagger-3.0.2.tar.gz file)
* lemma_metrics.py - Generates the accurracy and F1-score for the lemmatization files
  
**Test CSVS:**  
* Holds the accuracy CSVs for accuracy metrics to be generated on the lemmas as well as the original file with the correct lemmas for each word.
* The original correct lemmas were generated by running Wordnet on the text to get estimated correct lemmas and then combing through all of the lemmas to make sure they were valid.
* **Note:** To generate these accuracy files, the correct lemmas, generated lemmas, and original words were all copied from their respective files and placed into the accuracy file. Then, the lemmas were combed through to make sure the spacing matched up with the correct lemmas. If the lemmas for a specific lemmatizer were correct but followed a different convention, the correct lemmas were changed accordingly. This is why different accuracy spreadsheets have different numbers of horizontal rows.
  
**Unused Files:**
* Ignore these files
