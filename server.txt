const express = require('express');
const axios = require('axios');
const cors = require('cors');
require('dotenv').config();

const app = express();
const PORT = process.env.PORT || 5000;

app.use(cors());

app.get('/api/weather', async (req, res) => {
    const { lat, lon, location } = req.query;
    let url = `http://api.openweathermap.org/data/2.5/onecall?units=metric&appid=${process.env.WEATHER_API_KEY}`;

    if (lat && lon) {
        url += `&lat=${lat}&lon=${lon}`;
    } else if (location) {
        url = `http://api.openweathermap.org/data/2.5/weather?q=${location}&units=metric&appid=${process.env.WEATHER_API_KEY}`;
    } else {
        return res.status(400).json({ error: 'Location or coordinates are required' });
    }

    try {
        const response = await axios.get(url);
        res.json(response.data);
    } catch (error) {
        res.status(500).json({ error: 'Failed to fetch weather data' });
    }
});

app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
