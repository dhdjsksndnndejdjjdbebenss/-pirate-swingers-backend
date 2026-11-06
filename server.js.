const express = require('express');
const axios = require('axios');
const app = express();
app.use(express.json());

// Get credentials from Render Environment Variables
const PLAYFAB_TITLE_ID = process.env.PLAYFAB_TITLE_ID;
const PLAYFAB_SECRET_KEY = process.env.PLAYFAB_SECRET_KEY;
const PHOTON_APP_ID = process.env.PHOTON_APP_ID;
const PHOTON_APP_SECRET = process.env.PHOTON_APP_SECRET;

// Health check
app.get('/', (req, res) => res.send('Pirate Swingers Backend is Live.'));

// The login endpoint
app.post('/api/LoginWithCustomID', async (req, res) => {
    const { CustomId } = req.body;
    if (!CustomId) return res.status(400).json({ error: "Missing CustomId" });

    try {
        // 1. Login to PlayFab
        const playfabRes = await axios.post(`https://${PLAYFAB_TITLE_ID}.playfabapi.com/Client/LoginWithCustomID`, {
            TitleId: PLAYFAB_TITLE_ID,
            CustomId: CustomId,
            CreateAccount: true
        });
        const sessionTicket = playfabRes.data.data.SessionTicket;

        // 2. Generate Photon Auth Token
        const photonRes = await axios.post(`https://api.photonengine.com/api/v1/Authenticate`, {
            AppId: PHOTON_APP_ID,
            AppVersion: "1.0",
            UserId: CustomId
        }, {
            headers: { 'X-SecretKey': PHOTON_APP_SECRET }
        });

        // 3. Send tokens back to the game
        res.json({
            PlayFabTicket: sessionTicket,
            PhotonToken: photonRes.data.Token
        });
    } catch (error) {
        console.error(error.response ? error.response.data : error.message);
        res.status(500).json({ error: "Authentication failed" });
    }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
