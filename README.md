# District 6 VBM Ballot Tracking Map

Orange County Commission District 6 walk-team canvassing tool (2026 Primary).

## Architecture

The app is a static split build: `index.html` (this repo) + 12 asset files that exceed GitHub API push limits and are therefore stored in the campaign's self-hosted Supabase storage bucket (`d6`, public read) at `https://supabase.snapworxx.tools/storage/v1/object/public/d6/`:

- `app.js` — application code (map, walk list, VBM tracking, chase list, D6SYNC team sync)
- `leaflet.js`, `cluster.js` — Leaflet 1.9.4 + MarkerCluster 1.5.3 (inlined builds)
- `xlsx.js` — SheetJS 0.20.3 for daily county VBM file parsing
- `d_v0..2.js` — voter roll (5,698 voters, 3 chunks)
- `d_b0..3.js` + `d_meta.js` — VBM baseline (5,799 ballots, 4 chunks + metadata)

## Team sync

Contact marks (Reached / Not home / Moved) sync across all walker phones every 5 seconds via the self-hosted Supabase (`d6_contacts` table, anon insert/update/select only, delete revoked, RLS enabled). Offline taps queue in localStorage and drain on reconnect. Daily county file loads log a row to `d6_daily_snapshots`.

## Deployment

Served at https://district6.helpflavote.com. Deployment pulls all 13 files from the storage bucket; updating any file in the bucket + redeploying refreshes the live site.
