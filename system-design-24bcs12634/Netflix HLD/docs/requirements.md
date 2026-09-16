# Comprehensive System Requirements

To design a large-scale streaming platform like Netflix, we must clearly define two categories of needs: what the system does for the user (Functional) and how the system behaves under massive scale pressure (Non-Functional).

### 1. Functional Requirements (What the User Experiences)
Functional requirements define the specific features and user flows built into the application software layer.
* **Account and Profile Management:** 
  * Users must be able to create secure accounts, log in, and manage credit card billing details.
  * A single primary account must support up to 5 distinct individual user profiles (e.g., Mom, Dad, Kids) to keep watch histories separated.
* **Content Catalog and Search Discovery:** 
  * Users must be able to browse dynamic rows of movies categorized by genres (Action, Comedy, Drama) and trending lists.
  * The system must provide an autocomplete search bar that returns accurate movie titles, directors, or actors in under 50 milliseconds.
* **Seamless Video Playback Control:** 
  * The core video player must support basic media controls: Play, Pause, Skip 10 seconds, Volume Control, and Subtitle toggles.
  * The video client must remember exactly where a user stopped watching a video so they can "Resume Playback" from that exact second on any other device.

### 2. Non-Functional Requirements (How the Architecture Performs)
Non-functional requirements act as technical quality metrics. They dictate how the underlying cloud infrastructure survives traffic spikes.
* **High Availability (Fault Tolerance):** 
  * The platform's browsing and login services must maintain a 99.99% uptime status (known as "Four Nines"). This means the platform cannot experience more than 52.6 minutes of total unplanned downtime across an entire year.
* **Ultra-Low Playback Latency:** 
  * When a user clicks the "Play" button, the initial video startup delay (time-to-first-frame) must be less than 200 milliseconds. Long loading spinners cause users to leave the app.
* **Global Scale and High Concurrency:** 
  * The system must handle millions of concurrent video streams smoothly. If an incredibly popular show releases a new season at midnight, the system must scale automatically to prevent crashes.
* **Adaptive Bitrate Streaming (ABR):** 
  * The system must automatically detect changes in the user's internet speed in real time. If internet speeds drop, the player must seamlessly drop the video resolution (e.g., from 4K down to 720p) instead of freezing or buffering.
