# Project-1
// server.js
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');

const app = express();
const PORT = process.env.PORT || 3000;

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/eventsDB', { useNewUrlParser: true, useUnifiedTopology: true });

// Create Event Schema
const eventSchema = new mongoose.Schema({
    title: String,
    date: Date,
    location: String
});

const Event = mongoose.model('Event', eventSchema);

app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.json());

// Route to handle event creation
app.post('/create-event', (req, res) => {
    const { title, date, location } = req.body;

    const newEvent = new Event({
        title,
        date,
        location
    });

    newEvent.save((err) => {
        if (err) {
            res.status(500).send('Error saving event');
        } else {
            res.status(201).send('Event created successfully');
        }
    });
});

app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Event Listing</title>
</head>
<body>
    <h1>Event Listing</h1>
    
    <ul id="event-list"></ul>

    <script src="app.js"></script>
</body>
</html>
// app.js
document.addEventListener('DOMContentLoaded', () => {
    // Fetch events from the server and populate the listing page
    fetch('/get-events')
        .then(response => response.json())
        .then(events => {
            const eventList = document.getElementById('event-list');

            events.forEach(event => {
                const listItem = document.createElement('li');
                listItem.textContent = `${event.title} - ${new Date(event.date).toDateString()} - ${event.location}`;
                eventList.appendChild(listItem);
            });
        })
        .catch(error => console.error('Error fetching events:', error));
});
