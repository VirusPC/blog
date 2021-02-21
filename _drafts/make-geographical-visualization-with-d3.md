Create Geographic Visualizations with D3
===

There are two methods to create a geographic visualization:
*d3.geo* and *d3.tile*

d3.geo
---

### Data

The required data format is [GeoJSON](), if you want to use [TopoJSON]() instead, use [topojson](https://github.com/topojson/topojson) to translate it first:

```js
const geojsonData = topojsono.feature(topojsonData, topojsonData.objects.countries);
```

### Data Visualization

First, you neet create a *Projection* object, which used to project the original map to different surface. The projection methods varied, e.g., Mera





d3.tile
---