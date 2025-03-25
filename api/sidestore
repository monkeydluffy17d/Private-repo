const fetch = require("node-fetch");
const fs = require("fs");
const path = require("path");

const IPA_FOLDER = path.join(__dirname, "../uYouPlus"); // Change if needed

module.exports = async (req, res) => {
    try {
        // Fetch latest EVE Spotify release
        const response = await fetch("https://api.github.com/repos/whoeevee/EeveeSpotify/releases/latest");
        const data = await response.json();
        const ipaAsset = data.assets.find(asset => asset.name.endsWith(".ipa"));

        if (!ipaAsset) {
            return res.status(404).json({ error: "No .ipa file found in latest EVE Spotify release." });
        }

        // Read manually uploaded uYouPlus IPA
        const files = fs.readdirSync(IPA_FOLDER).filter(file => file.endsWith(".ipa"));
        files.sort((a, b) => b.localeCompare(a)); // Sort to get latest version
        const latestUYou = files[0];
        if (!latestUYou) {
            return res.status(404).json({ error: "No uYouPlus IPA found." });
        }

        const match = latestUYou.match(/uYouPlus_(\d+\.\d+\.\d+)-(\d+\.\d+\.\d+)\.ipa/);
        if (!match) {
            return res.status(400).json({ error: "Invalid uYouPlus filename format." });
        }

        const youtubeVersion = match[1];
        const uYouVersion = match[2];

        // Generate JSON response
        res.json({
            "name": "My SideStore Repo",
            "identifier": "com.mysidestore.repo",
            "apps": [
                {
                    "name": "EVE Spotify",
                    "bundleIdentifier": "com.spotify.client",
                    "developerName": "EVE Devs",
                    "version": data.tag_name,
                    "versionDate": data.published_at,
                    "versionDescription": data.body || "Latest version of EVE Spotify.",
                    "downloadURL": ipaAsset.browser_download_url,
                    "iconURL": "https://yourwebsite.com/evespotify-icon.png",
                    "tintColor": "#1DB954"
                },
                {
                    "name": `uYouPlus ${youtubeVersion} - ${uYouVersion}`,
                    "bundleIdentifier": "com.uyouplus.client",
                    "developerName": "uYou Devs",
                    "version": `${youtubeVersion}-${uYouVersion}`,
                    "versionDate": new Date().toISOString(),
                    "versionDescription": "Latest manually uploaded version.",
                    "downloadURL": `https://yourwebsite.com/uYouPlus/${latestUYou}`,
                    "iconURL": "https://yourwebsite.com/uyouplus-icon.png",
                    "tintColor": "#FF0000"
                }
            ]
        });
    } catch (error) {
        res.status(500).json({ error: "Failed to generate JSON." });
    }
};
