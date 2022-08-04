# Enabling Uncensored DNS over HTTPS on Edge
> **Warning: This guide will only route DNS traffic from Edge over HTTPS. This means that other DNS requests made by other applications on your system will be made without encryption. For system-wide secure DNS, please see our other guides.**

1. Open Edge and click the hamburger menu, then choose settings:
![Click Hamburger Menu, Choose Settings](screenshots/edge_1.png?raw=true "Edge Hamburger Menu")
2. Click "_**Privacy, Search and Services**_".
![Click Privacy, Search and Services](screenshots/edge_2.png?raw=true "Click Privacy, Search and Services")
3. Scroll down to _Security_
![Scroll Down](screenshots/edge_3.png?raw=true "Scroll Down")
4. Switch on "_**Use secure DNS to specify how to lookup the network address for websites**_".
5. Choose "_**Choose a service provider**_".
6. In the text field under "_Choose a service provider_", enter ´anycast.censurfridns.dk´
7. Click on the gray background surrounding the options page.
![Enable DNS Over HTTPS, select custom provider and enter URL](screenshots/edge_4.png?raw=true "Enable DNS Over HTTPS, select custom provider and enter URL")
8. Click "**_OK_**" and restart Edge
