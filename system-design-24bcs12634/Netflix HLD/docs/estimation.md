# System Scale and Infrastructure Capacity Estimation

To build a reliable system design, engineers use **Back-of-the-Envelope Calculations**. This simple math proves exactly *why* our system requires specific components—like a Content Delivery Network (CDN) or NoSQL caches—instead of relying on standard server builds.

### 1. System Traffic Assumptions
* **Total Global Registered Users:** 300 Million accounts.
* **Daily Active Users (DAU):** 100 Million unique viewers use the system daily.
* **Peak Concurrency Metric:** At peak viewing hours (usually 8 PM to 10 PM), assume 10% of your daily active users stream videos simultaneously.
  $$\text{100 Million DAU} \times 0.10 = \mathbf{10\text{ Million Concurrent Live Streams}}$$

---

### 2. Video Storage Capacity Math (The Data Warehouse size)
Videos are massive files. They aren't just stored once; a single movie is transcoded into multiple resolutions (4K, 1080p, 720p, 480p) and split into thousands of 3-second segments.
* **Total Media Library Target:** 10,000 video titles.
* **Average Storage Size per Movie:** Combining all resolutions, formats, and audio tracks, a single movie averages about 10 Gigabytes (GB).
* **Total Core Storage Calculations:**
  $$10,000 \text{ titles} \times 10\text{ GB per title} = 100,000\text{ GB} = \mathbf{100\text{ Terabytes (TB)}}$$
* **Conclusion:** 100 TB can easily fit inside cloud object storage warehouses like Amazon S3, using automated cross-region duplication for safety.

---

### 3. Network Bandwidth and Egress Capacity Math
This is the most critical metric. It calculates how much data leaves our cloud data centers every second when users stream.
* **Average Video Stream Bitrate:** Standard high-definition 1080p playback requires a data transmission speed of roughly 5 Megabits per second (Mbps) per user.
* **Peak System Network Outflow (Egress):**
  $$10\text{ Million concurrent users} \times 5\text{ Mbps per user} = \mathbf{50\text{ Terabits per second (Tbps)}}$$

### 💡 The Big Architectural Takeaway for Your Class
A standard server network port can only handle about 10 to 40 Gigabits per second (Gbps). Our system requires **50,000 Gigabits per second (50 Tbps)** at peak times! 

If we tried to stream all this video data out of our main central databases, the entire internet connection to our platform would immediately crash. 

**This math proves why our Video Delivery Pipeline is completely separated.** By caching the video segments on a global network of **CDN edge servers** placed right inside local internet service provider data hubs down the street from the users, 95% of this massive 50 Tbps bandwidth load is handled away from our core microservices. This keeps the central system fast, stable, and incredibly cost-effective.
