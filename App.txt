import React, { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
    const [location, setLocation] = useState('');
    const [weatherData, setWeatherData] = useState(null);
    const [error, setError] = useState(null);

    useEffect(() => {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(position => {
                const { latitude, longitude } = position.coords;
                fetchWeatherDataByCoords(latitude, longitude);
            }, () => {
                setError('Location access denied.');
            });
        }
    }, []);

    const fetchWeatherDataByCoords = async (lat, lon) => {
        try {
            const response = await axios.get(`http://localhost:5000/api/weather?lat=${lat}&lon=${lon}`);
            setWeatherData(response.data);
        } catch (error) {
            setError('Failed to fetch weather data.');
        }
    };

    const fetchWeatherDataByLocation = async () => {
        try {
            const response = await axios.get(`http://localhost:5000/api/weather?location=${location}`);
            setWeatherData(response.data);
        } catch (error) {
            setError('Failed to fetch weather data.');
        }
    };

    const handleInputChange = (e) => {
        setLocation(e.target.value);
    };

    const handleFormSubmit = (e) => {
        e.preventDefault();
        fetchWeatherDataByLocation();
    };

    return (
        <div>
            <h1>Weather Forecast</h1>
            <form onSubmit={handleFormSubmit}>
                <input type="text" value={location} onChange={handleInputChange} placeholder="Enter location" />
                <button type="submit">Get Weather</button>
            </form>
            {error && <p>{error}</p>}
            {weatherData && (
                <div>
                    <h2>Current Weather</h2>
                    <p>Temperature: {weatherData.current.temp}°C</p>
                    <p>Condition: {weatherData.current.weather[0].description}</p>
                    <h2>5-Day Forecast</h2>
                    {/* Display 5-day forecast */}
                </div>
            )}
        </div>
    );
}

export default App;
