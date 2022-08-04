# Enabling Uncensored DNS over HTTPS on Firefox
> **Warning: This guide will only route DNS traffic from Firefox over HTTPS. This means that other DNS requests made by other applications on your system will be made without encryption. For system-wide secure DNS, please see our other guides.**

1. Open Firefox and click the hamburger menu:
![Click Hamburger Menu](screenshots/ff_1.png?raw=true "Firefox Hamburger Menu")
2. Select "_Settings_":
![Select Settings](screenshots/ff_2.png?raw=true "Select Settings")
3. Scroll down to the bottom of the page
![Scroll Down](screenshots/ff_3.png?raw=true "Scroll Down")
4. Click the "_**Settings**_" button under _Network Settings_.
![Click Settings under Network Settings](screenshots/ff_4.png?raw=true "Click Settings under Network Settings")
5. Tick "_**Enable DNS over HTTPS**_" if it is not already ticked.
6. Under the "_Use Provider_" dropdown menu, select "_**Custom**_".
7. In the "_Custom_" text field, enter ´anycast.censurfridns.dk´
![Enable DNS Over HTTPS, select custom provider and enter URL](screenshots/ff_5.png?raw=true "Enable DNS Over HTTPS, select custom provider and enter URL")
8. Click "**_OK_**" and restart Firefox 
