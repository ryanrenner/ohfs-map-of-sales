# Omaha Homes Map — How This Works

This folder holds everything needed to run the "homes I've helped buy and sell" map on your website.

---

## What each file does

| File | What it is |
|---|---|
| `data/full-address.csv` | Your list of property addresses |
| `geocode.py` | A script that converts addresses into map coordinates |
| `locations.js` | The coordinates file the map reads (auto-generated — don't edit) |
| `index.html` | The actual map page hosted on GitHub |

**The basic idea:** You keep your addresses in the CSV. You run a script that converts them into coordinates. The map reads those coordinates. You never have to touch the map code itself.

---

## First-time setup

### What you need before you start

- [ ] A Google Maps API key (you already have this)
- [ ] Python 3 installed on your computer (check by opening Terminal and typing `python3 --version`)
- [ ] A GitHub account
- [ ] The `requests` Python package (you'll install it in step 2)

---

### Part 1 — Add your API key to the files

You need to put your Google Maps API key in two places.

**In `geocode.py`:**

1. Open `geocode.py` in any text editor
2. Find this line near the top:
   ```
   API_KEY = os.environ.get("GOOGLE_MAPS_KEY", "YOUR_API_KEY_HERE")
   ```
3. Replace `YOUR_API_KEY_HERE` with your actual key — keep the quotes
4. Save the file

**In `index.html`:**

1. Open `index.html` in any text editor
2. Use Find (Cmd+F) to search for: `YOUR_GOOGLE_MAPS_API_KEY`
3. Replace it with your actual key — keep the quotes
4. Save the file

---

### Part 2 — Run the geocoding script (one time)

This converts all your addresses into coordinates and writes `locations.js`.

1. Open Terminal
2. Navigate to this folder:
   ```
   cd "/Users/ryanrenner/Claude App Folder/google-maps-embed"
   ```
3. Install the required Python package (only need to do this once):
   ```
   python3 -m pip install requests --break-system-packages
   ```
   > **Note:** The `--break-system-packages` flag is required on modern Macs with Homebrew-managed Python. Despite the name, it is safe to use for a common package like `requests`.
4. Run the script:
   ```
   python3 geocode.py
   ```
5. Wait for it to finish — it will print each address as it goes. With ~250 addresses it takes about 15 seconds.
6. When it says "Done" you're ready for the next part.

> **If it says "Set your API key":** Go back to Part 1 and make sure you replaced the placeholder text in `geocode.py`.

> **If it says "over quota":** Your Google Cloud billing may not be enabled. The geocoding run costs about $1.25 total — you need a billing account set up even if you're within the free tier.

---

### Part 3 — Put the map on GitHub Pages

GitHub Pages hosts your map for free. You only do this setup once.

1. Go to [github.com](https://github.com) and sign in
2. Click the **+** in the top right corner, then **New repository**
3. Name it something like `omaha-homes-map`
4. Set it to **Public**
5. Click **Create repository**
6. Upload these files to the repo (drag and drop in the browser, or use GitHub Desktop):
   - `index.html`
   - `locations.js`
   - The `data/` folder (with `full-address.csv` inside)
7. Click **Commit changes** (or **Upload files** then commit)
8. Go to **Settings** (tab at the top of your repo)
9. Click **Pages** in the left sidebar
10. Under "Branch," select **main** and click **Save**
11. Wait about 1 minute, then your map is live at:
    ```
    https://YOUR-GITHUB-USERNAME.github.io/omaha-homes-map/
    ```

---

### Part 4 — Embed the map on your website

1. Copy this snippet:
   ```html
   <iframe src="https://YOUR-GITHUB-USERNAME.github.io/omaha-homes-map/" width="100%" height="520" style="border:0;display:block;border-radius:6px;" loading="lazy" title="Homes Ryan Has Helped Buy and Sell in Omaha"></iframe>
   ```
2. Replace `YOUR-GITHUB-USERNAME` with your actual GitHub username
3. Paste it into the WYSIWYG editor on your REW page where you want the map to appear

---

### Part 5 — Lock down your API key (important)

This prevents other people from using your API key and running up your bill.

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Click **APIs & Services** then **Credentials**
3. Click on your Maps JavaScript API key
4. Under "Application restrictions," choose **HTTP referrers**
5. Add these two entries:
   ```
   https://YOUR-GITHUB-USERNAME.github.io/*
   https://omahahomesforsale.com/*
   ```
6. Save

Do the same for your Geocoding API key, but restrict it to your IP address instead (since you only use it from your computer).

---

## Adding new addresses later

This is the only thing you'll do on a regular basis.

1. Open `data/full-address.csv`
2. Add the new address in a new row — follow the same format as the existing rows:
   ```
   "123 Main St Omaha, NE 68102",123 Main St,NE,Omaha
   ```
3. Save the CSV
4. Open Terminal and run:
   ```
   cd "/Users/ryanrenner/Claude App Folder/google-maps-embed"
   python3 geocode.py
   ```
5. When it finishes, upload the new `locations.js` to your GitHub repo (replace the old one)
6. GitHub Pages rebuilds automatically — the live map updates within 1 minute

That's it. You don't need to touch `index.html` or any other file.

---

## Troubleshooting

**The map shows "No locations loaded"**
The `locations.js` file is empty. Run `python3 geocode.py` and re-upload `locations.js` to GitHub.

**The map shows a gray box / loads forever**
Your API key in `index.html` is missing or wrong. Open `index.html`, find `YOUR_GOOGLE_MAPS_API_KEY`, and make sure it's replaced with your real key.

**An address didn't get a pin**
The script prints `[no results]` for addresses it couldn't geocode. Usually this means a typo in the address. Fix it in the CSV and re-run the script.

**The script errors with "OVER_QUERY_LIMIT"**
Your Google Cloud billing isn't set up, or your key doesn't have the Geocoding API enabled. Check the Google Cloud Console.

---

## Quick reference — the commands you'll use most

```bash
# Go to the project folder
cd "/Users/ryanrenner/Claude App Folder/google-maps-embed"

# Re-geocode after adding addresses
python3 geocode.py
```

After that: upload `locations.js` to GitHub. Done.
