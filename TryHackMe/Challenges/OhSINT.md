
# [TryHackMe Write-Up : OhSINT](https://tryhackme.com/room/ohsint)


---

## **Room Objective**

The goal of this room is to use **OSINT (Open Source Intelligence)** to **gather information about a person** by exploiting publicly available data.

---

## **Step 1: Analyze the “WindowsXP.jpg” Image**

- Download the image provided in the room.
- Check its **metadata** using **ExifTool**:

    ```bash
    exiftool.exe WindowsXP.jpg
    ```

- The result shows that the image contains **copyright information and GPS coordinates**.

---

## **Step 2: Find the User’s Avatar**

- A web search for **"OWoodflint"** reveals:
  - A **Twitter** account   
  - A **GitHub** account  
  - A **WordPress** blog 

- On **Twitter**, the user’s profile picture is a **cat** 🐱  
  **Answer Q1: "cat"**

---

## **Step 3: Locate the User via BSSID**

- One o the tweets contains a **BSSID**: `B4:5D:50:AA:86:41`
- Searching the BSSID on **[WiGLE.net](https://wigle.net)** 
  - The result points to **London** 🏙  
 **Answer Q2: "London"**

---

## **Step 4: Find the Wi-Fi SSID**

- On WiGLE, the **SSID (Wi-Fi name)** associated with that BSSID is displayed.  
 **Answer Q3: "UnileverWiFi"**

---

## **Step 5: Find the Email Address**

- On **GitHub** (`github.com/OWoodfl1nt/people_finder`), the email address listed is:  
  [**OWoodflint@gmail.com**](mailto:OWoodflint@gmail.com)

 **Answer Q4: "OWoodflint@gmail.com"**  
 **Answer Q5: "GitHub"**

---

## **Step 6: Discover the Vacation Destination**

- On the **WordPress blog** (http://oliverwoodflint.wordpress.com/), the user mentions a trip to **New York**.  
 **Answer Q6: "New York"**

---

## **Step 7: Find the Password**

- Viewing the **source code** of the WordPress site reveals **hidden white text** containing the password.
- Select all using `CTRL + A` to make it visible.  
 **Answer Q7: `pennYDr0pper.!`**

---

## **Final Summary**

- **OSINT can reveal a surprising amount of public information** — location, Wi-Fi, email, travels, and more.  
- **File metadata** can be critical and may expose **GPS coordinates**.  
- **WiGLE.net** is valuable for mapping **BSSID and Wi-Fi networks**.  
- **Inspecting webpage source code** can uncover hidden secrets.
