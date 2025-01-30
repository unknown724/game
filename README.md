from flask import Flask, render_template_string
import folium

app = Flask(__name__)

# Most visited cities data
most_visited_cities = [
    {"rank": 1, "city": "Bangkok", "country": "Thailand", "visitors": 22.78, "lat": 13.7563, "lon": 100.5018},
    {"rank": 2, "city": "Paris", "country": "France", "visitors": 19.10, "lat": 48.8566, "lon": 2.3522},
    {"rank": 3, "city": "London", "country": "United Kingdom", "visitors": 19.09, "lat": 51.5074, "lon": -0.1278},
    {"rank": 4, "city": "Dubai", "country": "UAE", "visitors": 15.93, "lat": 25.276987, "lon": 55.296249},
    {"rank": 5, "city": "Singapore", "country": "Singapore", "visitors": 14.67, "lat": 1.3521, "lon": 103.8198},
    {"rank": 6, "city": "New York", "country": "USA", "visitors": 13.60, "lat": 40.7128, "lon": -74.0060},
    {"rank": 7, "city": "Kuala Lumpur", "country": "Malaysia", "visitors": 13.43, "lat": 3.1390, "lon": 101.6869},
    {"rank": 8, "city": "Tokyo", "country": "Japan", "visitors": 12.93, "lat": 35.682839, "lon": 139.759455},
    {"rank": 9, "city": "Istanbul", "country": "Turkey", "visitors": 12.80, "lat": 41.0082, "lon": 28.9784},
    {"rank": 10, "city": "Seoul", "country": "South Korea", "visitors": 11.25, "lat": 37.5665, "lon": 126.9780},
]

# Create a folium map
world_map = folium.Map(location=[20, 0], zoom_start=2, tiles="cartodb positron")

# Add city markers
for city in most_visited_cities:
    popup_text = f"""
    <b>{city['city']}, {city['country']}</b><br>
    🌍 Rank: {city['rank']}<br>
    👥 Visitors: {city['visitors']} Million
    """
    folium.Marker(
        location=[city["lat"], city["lon"]],
        popup=folium.Popup(popup_text, max_width=300),
        tooltip=f"{city['city']} (Click for details)",
        icon=folium.Icon(color="blue", icon="cloud")
    ).add_to(world_map)

# Get map HTML
map_html = world_map._repr_html_()

# Flask Route
@app.route('/')
def home():
    return render_template_string("""
    <html>
        <head>
            <title>Most Visited Cities</title>
            <meta name="viewport" content="width=device-width, initial-scale=1">
            <style>
                body { font-family: Arial, sans-serif; text-align: center; }
                h2 { color: #2C3E50; }
                .map-container { width: 100%; height: 600px; }
                iframe { width: 90%; height: 90vh; border: none; }
                .share-btn {
                    background: #25D366; color: white; padding: 10px 20px; border: none;
                    font-size: 16px; cursor: pointer; border-radius: 5px;
                }
            </style>
        </head>
        <body>
            <h2>Interactive World Map</h2>
            <button class="share-btn" onclick="shareOnWhatsApp()">📲 Share on WhatsApp</button>
            <br><br>
            {{ map_html|safe }}
            <script>
                function shareOnWhatsApp() {
                    let text = "Check out the most visited cities: https://yourappname.onrender.com";
                    let url = "https://wa.me/?text=" + encodeURIComponent(text);
                    window.open(url, "_blank");
                }
            </script>
        </body>
    </html>
    """, map_html=map_html)

# Run Flask App
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
