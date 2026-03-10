# GFSView: Serverless Generic NWP Globe (WASM)

[Live Demo](https://coliveir-aer.github.io/gfsview/) | [GitHub Repository](https://github.com/coliveir-aer/gfsview/)

**GFSView** is a 100% client-side, serverless web application that visualizes Numerical Weather Prediction (NWP) data directly in the browser. It leverages WebAssembly (WASM) to decode NOAA's Global Forecast System (GFS) GRIB2 files on the fly and renders them onto an interactive 3D globe or 2D map using Three.js.

### [**Launch the Live Demo**](https://coliveir-aer.github.io/gfsview/)

## Key Features

* **Zero Backend Infrastructure:** The application fetches forecast data directly from NOAA's public AWS S3 buckets (`noaa-gfs-bdp-pds`). No intermediate server is required.
* **Smart Data Extraction:** By reading the `.idx` inventory files first, the app uses **HTTP Range Requests** to download only the specific byte ranges required for your selected variable and vertical level. This reduces data transfer from gigabytes down to mere kilobytes.
* **In-Browser GRIB2 Decoding:** Uses a custom WebAssembly (WASM) build of `g2clib` to parse and decode complex meteorological GRIB2 data purely on the client side.
* **Dynamic Rendering:**
  * **Scalar Fields:** Renders heatmaps with dynamically scaled legends for temperature, humidity, precipitation, cloud cover, etc.
  * **Vector Fields:** Automatically synthesizes U and V wind components to generate a scalable, dynamic Wind Barb field.
* **Interactive Exploration:** Switch seamlessly between a 3D Earth and a 2D Flat Map. Click anywhere on the map to raycast and view the exact data values (Lat/Lon/Value) for that location.

## How It Works

1. **Load NWP Inventory:** The user selects a date, model cycle (e.g., 12Z), resolution, and forecast hour. The app fetches the corresponding `.idx` file from AWS S3.
2. **Parse Variables:** The index file is parsed into a local database of available products (TMP, WIND, PRATE, etc.) and their vertical levels (Surface, 500mb, etc.).
3. **Fetch & Render:**
   * When a variable is requested, the app calculates the byte offset from the index and executes an HTTP Range Request.
   * The downloaded GRIB2 binary chunk is passed into WebAssembly memory.
   * `g2clib.wasm` decodes the payload and returns a JSON metadata object and a Float32Array of grid values.
   * Three.js converts the Float32Array into an HTML5 Canvas texture using dynamic colormaps and applies it to the globe geometry.

## Running Locally

Because this project relies on WebAssembly (`g2clib.wasm`) and fetch requests, it must be served over a local web server (opening the `index.html` directly via `file://` will result in CORS/WASM execution errors).

1. Clone the repository:
   ```bash
   git clone [https://github.com/coliveir-aer/gfsview.git](https://github.com/coliveir-aer/gfsview.git)
   cd gfsview
   ```

2. Start a local web server. For example, using Python 3:
   ```bash
   python -m http.server 8000
   ```
   *Alternatively, you can use Node's `http-server` or VS Code Live Server.*

3. Open your browser and navigate to `http://localhost:8000`.

## Dependencies

This project is built to be extremely lightweight and relies on minimal external libraries:
* **[Three.js (r128)](https://threejs.org/)**: For WebGL 3D/2D rendering.
* **`g2clib.wasm` / `g2clib.js`**: Custom WebAssembly compilation of the NCEP GRIB2 C library (included in the repository).

## Data Source
All meteorological data is sourced publicly from the **NOAA Global Forecast System (GFS)** via the [AWS Open Data Registry](https://registry.opendata.aws/noaa-gfs-bdp-pds/).


if __name__ == "__main__":
    print(readme_markdown)
