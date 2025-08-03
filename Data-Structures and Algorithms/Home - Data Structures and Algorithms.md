


1. High level application diagram, functional architeture, get an alignment
2. Decide on the MVP features, version the project
3. Decide on the tech in each component 
4. Set up expectations for each block
5. Propose a folder structure
6. Ensure Github
7. Make a high level functional architecture
8. Segments of market "Tech illiterate" and "Tech Capable"
9. Focus on Urban: Phase 1, then town, then rural. 
10. Starting with late night working women. Software + Hardware in the first version only. MVP is still tweakable.
11. Later focus on GSM devices, phone free.
12. Bluetooth device: emergency, smartphone app: setup bluetooth device and tertiary functions (user contact), cloud: data churning and user storage
13. Cloud data can be used to provide evidence to the authority. 
14. App needs to be able to connect to multiple device providers. Focus on service oriented architecture.
15. Cloud architecture should be independent of the cloud service provider. #`Agnostic` behavior.
16. Hardware is being supplied already, focus on app
17. Use Kotlin, Swift for respective OSes. Decide later though
18. Start Functional architecture, Test the SDK and devices, decide on native or abstracted architecture.


# Functionalities 
## Pre-Distress
1. Map data for safe zone, unsafe zone
	1. crime data
	2. time
	3. lighting
	4. area category
	5. User reports about area
	6. Public events data - political rallies, concerts etc gathered from OSI
	7. Age of user
	Need to make dynamically updated map data
2. Guidance system, weighted shortest path calculation
3. communication service integration with whatsapp, messages
4. real-time location update of users and nearest user detection(unsure)
5. Tracking of followers: Not sure how will it be implemented, most mobile signals are frequency hopped, encrypted, so difficult to ID a particular signal.
6. User routes storage and deviation detection
7. User can report problematic areas

## During Distress
1. Manual trigger -> mobile device -> communication
2. Software trigger
	1. Voice trigger
	2. App SOS button
	3. Automatic alerts for high risk
	4. Responsive feedback: If the user fails to respond for some time in `red` areas, assume distress
3. Audio + video recording 
4. Server side audio classification : distress, shouting etc.
5. Environmental cues:
	1. Sudden path deviation:
		1. Prediction of path
