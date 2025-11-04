Fibre Availability API

This is a high-performance API built with FastAPI and GeoPandas to query fibre availability and proximity from a pre-loaded GeoJSON dataset.

It is designed to serve as a backend for applications needing to check for fibre connections near a specific geographic coordinate.

Features

Fast: Uses FastAPI for high-speed asynchronous requests.

Geospatial: Leverages GeoPandas and Shapely for powerful spatial queries.

Ready-to-use: Loads data on startup for low-latency queries.

Robust: Safely handles various geometry types (Points, Lines, Polygons) and missing data (NaN) for clean JSON responses.

Project Structure

.
├── main.py             # Your main FastAPI application
├── README.md           # This file
├── requirements.txt    # Python dependencies
└── utils/
    ├── __init__.py
    ├── loader.py       # (Placeholder) Logic to load your GeoJSON data
    └── geo.py          # (Placeholder) Logic for spatial queries


Setup and Installation

Clone the repository (or create the files):
Save the files provided (main.py, README.md, etc.) into a new project folder.

Create a Virtual Environment:

python3 -m venv venv
source venv/bin/activate


Install Dependencies:
Install all required libraries from the requirements.txt file.

pip install -r requirements.txt


Prepare Your Data:
This API is designed to load a GeoJSON file. You must:

Have a cached_osp_layers.geojson file (or similar).

Update the utils/loader.py file to load your actual data. The current file is just a placeholder for testing.

Prepare Your Spatial Functions:
The API calls utils.geo.nearest_feature. You must add your actual spatial query logic to utils/geo.py. The current file is just a placeholder.

Running the API

Once your dependencies are installed, you can run the server using uvicorn:

uvicorn main:app --reload


main: Refers to the file main.py.

app: Refers to the object app = FastAPI() inside main.py.

--reload: Enables hot-reloading for development.

The API will be available at http://127.0.0.1:8000.

API Endpoints

You can access the interactive documentation (provided by Swagger UI) at http://127.0.0.1:8000/docs.

GET /

Returns a simple welcome message and the total number of features loaded.

URL: http://127.0.0.1:8000/

Response:

{
  "message": "Welcome to Fibre Availability API",
  "total_features": 12345
}


GET /metadata

Returns the metadata loaded from the utils/loader.py script.

URL: http://127.0.0.1:8000/metadata

Response:

{
  "data_source": "cached_osp_layers.geojson",
  "last_updated": "2023-10-27T10:30:00Z",
  "crs": "EPSG:4326"
}


GET /availability

Finds the single nearest fibre feature to a given coordinate.

URL: http://127.0.0.1:8000/availability

Query Parameters:

lat (float, required): Latitude.

lon (float, required): Longitude.

Example: http://127.0.0.1:8000/availability?lat=-26.1952&lon=28.0341

Success Response (200):

{
  "nearest_id": 101,
  "distance_m": 45.7,
  "name": "DFA Building 101",
  "connected": true,
  "address": "123 Fake St, Johannesburg",
  "coordinates": [
    28.0345,
    -26.1954
  ]
}


Not Found Response (404):

{
  "detail": "No fibre feature found nearby"
}


GET /nearby

Finds all fibre features within a specified radius (in meters) of a coordinate.

URL: http://127.0.0.1:8000/nearby

Query Parameters:

lat (float, required): Latitude.

lon (float, required): Longitude.

radius_m (float, optional, default: 500): Search radius in meters.

Example: http://127.0.0.1:8000/nearby?lat=-26.1952&lon=28.0341&radius_m=250

Success Response (200):

{
  "count": 2,
  "results": [
    {
      "OBJECTID": 101,
      "Building_Name": "DFA Building 101",
      "DFA_Connected_Y_N": "Y",
      "Street_Address": "123 Fake St, Johannesburg",
      "layer": "DFA_Buildings",
      "geometry": [ 28.0345, -26.1954 ]
    },
    {
      "OBJECTID": 5002,
      "Duct_ID": "D-5002",
      "Type": "Fibre Duct",
      "Status": "Active",
      "layer": "Ductbank",
      "geometry": [ 28.0340, -26.1951 ]
    }
  ]
}


Not Found Response (404):

{
  "detail": "No nearby fibre features found"
}
