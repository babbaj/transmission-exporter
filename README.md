This is a fork of https://github.com/metalmatze/transmission-exporter, see the real README there.

I made these changes because the latency of fetching metrics was too slow. My changes improved that latency from ~2 minutes to ~2 seconds (I have several thousand Linux ISOs in Transmission).

Summary of changes made in this fork:

* Instead of grabbing every torrent, it uses the RPC query `"ids": "recently-active"` to only grab recently active torrents. It still grabs every torrent on the first run, then does recently active only from then on. A map is used to maintain torrents current status, the key is the torrent hash. This way the metrics stay available, they just don't change while the torrent is dormant. This is faster since Transmission doesn't serialize and send unchanged information.
* Fields for files, peers, and trackes are all removed. **These metrics are no longer exported,** and they are no longer requested as fields in RPC calls. So, **this fork has less functionality**, but it's faster. Those metrics aren't interesting anyway. :)
* New exported metric `uploaded_ever_bytes`. Technically you could compute this by multiplying the ratio by the size, but I would rather just export the actual integer. Transmission will tell you this if you ask, so `uploadedEver` was added to the list of fields requested from its RPC.
* `lastScrapeTimedOut` issue fixed by simply changing datatype in JSON struct from bool to int