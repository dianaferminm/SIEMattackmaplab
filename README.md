<h1>Azure SOC Honeypot with Sentinel</h1>

<h2>Description</h2>
This project walks through setting up a basic Security Operations Center (SOC) in Microsoft Azure from scratch. Using a free Azure subscription, it covers the creation of a virtual machine (VM) acting as a honeypot, forwarding logs to a central repository, and integrating Microsoft Sentinel for real-time threat detection and analysis. Through this project, you will learn how to:

- <b>Set up an Azure VM and configure it to act as a honeypot exposed to the internet.</b>
- <b>Create and configure a Log Analytics Workspace, and integrate it with Microsoft Sentinel.</b>
- <b>Forward logs from your VM, query for failed login attempts, and visualize attack data.</b>
- <b>Enrich logs with geographic information by using Sentinel Watchlists.</b>
- <b>Build an interactive attack map to track real-time hacker activity.</b>


<h2>Languages and Utilities Used</h2>

- <b>KQL (Kusto Query Language)</b> 
- <b>GeoIP Watchlist</b>
- <b>Terminal (macOS)</b>


<h2>Environments Used </h2>

- <b>Microsoft Azure</b>
- <b>Azure Portal</b>
- <b>Windows 10</b>
- <b>Microsoft Sentinel</b>
- <b>Azure Log Analytics Workspace</b>
- <b>macOS</b>

<h2>Overview</h2>
<b>This diagram shows the flow of an attack simulation in the home SOC setup. The attackers from the public internet attempt to breach the system. The Azure environment (including the VM and NSG) is configured to receive and capture these attacks. The logs are forwarded to Log Analytics Workspace, where they’re processed and sent to Microsoft Sentinel for analysis. Finally, the attack map visualizes the geographic locations and intensity of the attacks, giving a clear picture of the threat landscape.
</b>

![Azure Subscription](https://github.com/user-attachments/assets/64287b3c-56ee-46af-a6b8-36177f3d74e4)


<h2>Program walk-through:</h2>

1. **Go to Azure Portal** and search for **Resource Group**. Click **Create** and name the Resource Group.
2. Inside the Resource Group, go to **Virtual Network**. Set the **Resource Group** to the one you just created, and give the VNet a unique name. Click **Next** through the options and **Review + Create**.
3. Go to **Virtual Machine**. Set the **Resource Group** to the one you created earlier, and name the VM. Choose the **Windows 10 Pro image**, select **D2s_v3** for size, and configure the username and password.
4. Confirm the configuration and click **Next** through the options.
5. Under **Networking**, choose **Delete public IP** and continue.
6. Under **Diagnostics**, **Disable boot diagnostics**. Click **Review + Create** and then **Create**.
7. Once created, go back to the **Resource Group** to view the VM and associated resources.

<p align="center">

Overview of the Resource Group with key components: Virtual Machine, Public IP, Network Security Group, Network Interface, Disk, and Virtual Network: <br/>
![soclab1](https://github.com/user-attachments/assets/ee554043-c8ce-499b-96d6-0288d50e0e51)
<br />
<br />

8. Go to **Network Security Group** for `CORP-NET-EAST-1-nsp`.  
   **Explanation**: We're changing the inbound rules to allow all traffic.  
   - **Delete the inbound RDP rule**.  
   - Go to **Inbound Security Rules**, click **Add**, and set the following:  
     - **Source**: Any  
     - **Source port range**: Any  
     - **Destination**: Any  
     - **Destination port range**: Any  
     - **Action**: Allow  
     - **Name**: DANGER  
   - Click **Add**.

<img width="1419" alt="Inboundrule" src="https://github.com/user-attachments/assets/37dc27a6-cac1-4767-8cfc-2c9358f9ada2" />

9. Go to **Virtual Machine**, copy the **Public IP** address, and open the **Remote Desktop Virtual Machine** app.  
   - Add a new PC and paste the copied **IP address**.  
   - Start it up and log in with the username and password you created earlier.
<img width="1040" alt="Screenshot 2025-02-25 at 11 51 58 AM" src="https://github.com/user-attachments/assets/150818e8-e6bb-4227-a9d6-271acab4c34f" />

10. **Inside the VM**, search and open **wf.msc**, click **Windows Firewall Defender Proporties**, turn off the **Firewall State** for all 3 profiles Domain, Private & Public.  
    - Click **Apply** and **OK**.

![soclab2 (2)](https://github.com/user-attachments/assets/315d1b74-4929-47c2-bcf6-63e187239508)

11. **Back to your terminal on your device**:  
    - Ping the **Public IP** of the VM you copied earlier to verify connectivity.
   
![soclab2 (1406 x 1042 px)](https://github.com/user-attachments/assets/7e07ef67-2f2c-4061-95dc-2e3ab71479f5)

12. **Go back to the VM** and **fail the login** attempt a few times.  
    - Log in successfully with the credentials you created earlier.

![soclab2 (1406 x 1042 px) (2534 x 1394 px) (1920 x 1080 px)](https://github.com/user-attachments/assets/6595a9c4-e08d-4011-88d4-72959924157d)

13. **Inside the VM**, go to **Event Viewer**:  
    - Go to **Windows Logs** > **Security**.  
    - Filter logs by **Event ID 4625** to see the failed login attempts.

![soclab6 (2)](https://github.com/user-attachments/assets/77446ec4-15da-4428-bea2-8f2abd80aca7)

14. **Go back to Azure Portal** and search for **Log Analytics Workspace**.  
    - Create a new workspace and make sure to put it in the correct resource group.  
    - Name the workspace: `LAW-SOC-lab-0000`.

15. **Go to Microsoft Sentinel**:  
    - Add the workspace to **Sentinel**.  
    - In **Content Hub**, search for **Security Events** and install the **Windows Security Events** connector.

![soclab6 (1) (1)](https://github.com/user-attachments/assets/fa592200-edb9-403a-b542-2ee40f95ca21)

16. Once the connector is installed, go to **Manage** and check **Windows Security Events via AMA**.  
    - Click **Open Connector Page** and create a **Data Collection Rule (DCR)**.  
      - **Name**: DCR-Windows  
      - Click **Next** and **Expand**, then select the **VM**.  
      - Click **Next**, then **Review + Create**.

![soclab8](https://github.com/user-attachments/assets/93e3cde7-f103-4c0b-90d4-c8287fec8e09)

![soclab8 (2534 x 1394 px) (2534 x 1500 px)](https://github.com/user-attachments/assets/37fc2c73-68eb-4dd2-bdc5-e49be9d5d87d)

17. **Go to the Virtual Machine** settings, and under **Extensions**, check if **Windows AMA** is listed.

![soclab10](https://github.com/user-attachments/assets/f86d70d3-b0b2-4ae9-8c6b-45944de7943f)

18. **Go to Log Analytics Workspaces**:  
    - Open the workspace you created earlier.  
    - Click on **Logs**, type `SecurityEvent`, and run the query to see the logs.

![soclab10 (1)](https://github.com/user-attachments/assets/f831530a-df1e-47d5-8c3d-0ad4d70971d5)

   
19. **Observe the SecurityEvent logs**:  
    - You'll see that there is no location data, just IP addresses, which we can enrich with geographical information.

20. **Download the geoip-summarized.csv** file and import it as a **Sentinel Watchlist**:  
    - **Name/Alias**: geoip  
    - **Source Type**: Local File  
    - **Number of lines before row**: 0  
    - **Search Key**: network
    - Click **Review + Create**, then click **Create** 

![soclab10 (2)](https://github.com/user-attachments/assets/8d76bcf3-8075-4443-8434-a103b3867e07)

21. **Observe the logs now have geographic information**:  
    - This allows you to see where the attacks are coming from by mapping the IP addresses to locations.

![soclab10 (4)](https://github.com/user-attachments/assets/9128b71a-8883-414c-aaa6-7b861935603d)

22. **Run the following steps to enrich the logs with location data**:
- Paste the KQL query below, but **don’t run it yet**:
    ```
    let GeoIPDB_FULL = _GetWatchlist("geoip");
    let WindowsEvents = SecurityEvent
        | where IpAddress == "<attacker IP address>"
        | where EventID == 4625
        | order by TimeGenerated desc
        | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
    WindowsEvents
    ```
- Run the following query to filter for failed login events (Event ID 4625) and identify an attacker’s IP address:

    ```kusto
    SecurityEvent
    | where EventID == 4625
    ```
- Copy the attacker’s IP address from the results.
- Replace `<attacker IP address>` in the query with the copied IP (ensure it is in quotation marks), and then run the query to enrich the logs with location data.
- Add this to the query to **project** the relevant information such as the time, computer, attacker IP, and geographic details:

    ```kusto
    | project TimeGenerated, Computer, AttackerIp = IPaddress, cityname, countryname, latitude, longitude
    ```

![Untitled design (1)](https://github.com/user-attachments/assets/1c878477-055a-4438-93be-b020afa28e4f)

![Untitled design (4)](https://github.com/user-attachments/assets/ebf4a57c-c3cb-43bc-883a-34be229f0b68)


23. **In Microsoft Sentinel**, create a new **Workbook**.  
    - Delete any prepopulated elements, then add a new **Query** element.

![Untitled design (5)](https://github.com/user-attachments/assets/56435680-a887-4b9c-b65b-d99c0fa0b194)

24. **Go to the Advanced Editor Tab** and **Paste the following JSON for the Attack Map Workbook configuration**:

  ```json
{
	"type": 3,
	"content": {
		"version": "KqlItem/1.0",
		"query": "let GeoIPDB_FULL = _GetWatchlist(\"geoip\");\nlet WindowsEvents = SecurityEvent;\nWindowsEvents | where EventID == 4625\n| order by TimeGenerated desc\n| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)\n| summarize FailureCount = count() by IpAddress, latitude, longitude, cityname, countryname\n| project FailureCount, AttackerIp = IpAddress, latitude, longitude, city = cityname, country = countryname,\nfriendly_location = strcat(cityname, \" (\", countryname, \")\");",
		"size": 3,
		"timeContext": {
			"durationMs": 2592000000
		},
		"queryType": 0,
		"resourceType": "microsoft.operationalinsights/workspaces",
		"visualization": "map",
		"mapSettings": {
			"locInfo": "LatLong",
			"locInfoColumn": "countryname",
			"latitude": "latitude",
			"longitude": "longitude",
			"sizeSettings": "FailureCount",
			"sizeAggregation": "Sum",
			"opacity": 0.8,
			"labelSettings": "friendly_location",
			"legendMetric": "FailureCount",
			"legendAggregation": "Sum",
			"itemColorSettings": {
				"nodeColorField": "FailureCount",
				"colorAggregation": "Sum",
				"type": "heatmap",
				"heatmapPalette": "greenRed"
			}
		}
	},
	"name": "query - 0"
}
```

![Untitled design (6)](https://github.com/user-attachments/assets/ddc3ee59-6429-47fe-aa35-3305b3b57556)

26. Observe the Query and Map Settings:
- What the Query Does: It pulls up the failed login attempts (Event ID 4625), adds some geographic info (like country, city, latitude, and longitude), and then groups the failures by IP address. This gives you a clear view of where the attacks are coming from.
- What the Map Settings Do: These settings control how the attack data shows up on the map:
  - Location Info: It plots the attacks on the map using the latitude and longitude.
  - Color Settings: The heatmap will color the attack points, with red showing high failure counts and green for lower counts.
  - Size Settings: The bigger the point on the map, the higher the number of failures from that location.
- What You Can Change:
  - Play around with the opacity to make the map more or less transparent.
  - Switch the heatmap color palette (maybe go from blue to red).
  - Tweak the legend to show more info, like total failures or attack frequency.
---

## Finished!
You have successfully set up the home SOC environment, configured the VM as a honeypot, collected logs, enriched them with location data, and created an attack map to visualize real-time hacker activity.

</p>


<!--
 
diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@

--!>
