# How to turn your data into TopoJSON for mapping with D3

For those times when you just want some boxes and lines to tell you how to convert data for D3 maps.

**Full explanations, extra tips and links down below,** because "Convert to TopoJSON" is *kind of* a vague step.

## The Flow Chart

````
       +--------------------------------+           +--------------------------+
       |                                |           |                          |
       | Do you have a geographic file? +----NO---->| Go find one! D:          |
       |                                |           |                          |
       +--------------+-----------------+           +--------------------------+
                      |
                     YES
                      |
       +--------------v-----------------+           +--------------------------+
       |                                |           |                          |
       | Does your geographic file      |           | 1. Upload to Carto       |
  +----+ contain all of the information |           | 2. Download as GeoJSON   |
  |    | you want to map?               |           | 3. Convert to TopoJSON   |
  |    |                                |   +------>| 4. Ready to map!         |
  |    +--------------+-----------------+   |       |                          |
  |                   |                     |       +--------------------------+
NOPE                 YES                SHAPEFILE
  |                   |                     |       +--------------------------+
  |    +--------------v-----------------+   |       |                          |
  |    |                                +---+       | 1. Convert to TopoJSON   |
  |    | What format is your geographic |           | 2. Ready to map!         |
  |    | data in?                       +-GEOJSON-->|                          |
  |    |                                |           +--------------------------+
  |    +--------+------------+----------+
  |             |            |                      +--------------------------+
  |         TOPOJSON        CSV                     |                          |
  |             |            |                      | You can map lat/lon      |
  |             |            +--------------------->| pairs without any        |
  |             |                                   | converting               |
  |             |                                   |                          |
  |             |                                   +--------------------------+
  |             |
  |             |                                   +--------------------------+
  |             |                                   |                          |
  |             +---------------------------------->| You're ready to map!     |
  |                                                 |                          |
  |                                                 +--------------------------+
  |
  |    +--------------------------------+
  |    |                                |           +--------------------------+
  |    |  What kind of file has the     |           |                          |
  +---->  other information you'd like  +----GEO--->| Use d3.queue to import   |
       |  to map?                       |           | multiple files, or use   |
       |                                |           | geo2topo to combine many |
       +---------------+----------------+           | GeoJSON into 1 TopoJSON  |
                       |                            |                          |
                  NON-GEO CSV                       +--------------------------+
                       |
       +---------------v----------------+           +--------------------------+
       |                                |           |                          |
       | Does your CSV have a column    |           | Find another geographic  |
       | that will match with your      +---NO----->| dataset, or find another |
       | geographic data?               |           | CSV, or hand-edit the    |
       |                                |           | columns in either        |
       +---------------+----------------+           |                          |
                       |                            +--------------------------+
                      YES
                       |
       +---------------v----------------+           +--------------------------+
       |                                |           |                          |
       | What format is your            |           | 1. Upload both of your   |
       | geographic data in?            +-SHAPEFILE-> datasets to Carto        |
       |                                |           | 2. Perform a column join |
       +------+---------------+---------+           | between the two datasets |
              |               |                     | 3. Export as GeoJSON     |
           TOPOJSON        GEOJSON                  | 4. Convert to TopoJSON   |
              |               v                     | 5. Ready to map!         |
              |   +---------------------+           |                          |
              |   |                     |           +--------------------------+
              |   | Convert to TopoJSON |
              |   |                     |
              |   +-----------+---------+
              |               |
              v               v
       +--------------------------------+
       |                                |
       | Import your CSV and your       |
       | TopoJSON into D3, then use     |
       | d3.map() to join your          |
       | geography to the CSV data when |
       | drawing the map                |
       |                                |
       +--------------------------------+
````

## Explanations

### "What format is your geographic data in?"

* `.zip` is a shapefile
* `.shp` is a shapefile
* `.csv` is a csv
* `.json` could either be TopoJSON or GeoJSON. If you open up the .json and it's chock full of latitude and longitude it's probably GeoJSON. If it's a bunch of small integer numbers it's probably TopoJSON.

### "Upload to Carto"

Carto is at [carto.com](http://carto.com), and used to be known as CartoDB. It makes maps but is also pretty good for joining your data together.

If you're loading a shapefile, upload **the entire .zip, not just the .shp**

### "Perform a column join"

As long as your two datasets have a column in common you can join 'em together - I explain how to use Carto to do this in [an overly long video on YouTube](https://www.youtube.com/watch?v=NG8kdr2ICFQ&list=PLewNEVDy7gq1gyCsB3fysV1m-mHtyX2Jj) (I join a shapefile and a CSV)

### "Download as GeoJSON"

When looking at a Carto dataset, click **Edit** in the top right-hand corner, then **Export...**, and finally click **GeoJSON**.

### "Convert to TopoJSON"

**If you don't want to use the command line**, use [The Distillery](https://shancarter.github.io/distillery/)

**If you want to use the command line,** you can find instructions for converting from GeoJSON to TopoJSON using `geo2json` [over here](https://github.com/topojson/topojson). While the repository pretends it doesn't exist, older versions had a `topojson` command that did everything (joining datasets, converting from shapefiles, etc). These days you need to use something else to convert to GeoJSON before you convert to TopoJSON (such as Carto).

Frankly, even if you know what you're doing the Distillery might be a better choice since it gives you easy-to-use simplification options.

### "Import your CSV and your TopoJSON into D3"

Use `d3.queue` to pull them both in, make sure one is read in as JSON and the other as a CSV!

````javascript
d3.queue()
  .defer(d3.json, "geodata.json")
  .defer(d3.csv, "otherdata.csv")
  .await(ready)

function ready(error, geodata, otherdata) {
  ...
}
````

### "Use d3.map() to join your data..."

I'll make a video about this.

## Other tips

* If you'd like to know how to make a map, period, maybe check out [this YouTube playlist](https://www.youtube.com/playlist?list=PLewNEVDy7gq2-YdYlHxWwFDZHNN5hUIaY). It was just review for my class, though, so who knows how complete it is.
* If your CSV has addresses but no lat/lon, you need to **geocode** them. You can use [Geocod.io](https://geocod.io/) or [Carto](http://carto.com) for that.
* If you'd like to be a little more independent than relying on Carto, you can use [QGIS](http://qgis.org)
* If you'd like to draw your own map, use [geojson.io](http://geojson.io)
* If you want a map that slides and slides and zooms, just use [Carto](http://carto.com) if you like a really simple solution or [Leaflet](http://leafletjs.com/) if you like to customize things a bit more