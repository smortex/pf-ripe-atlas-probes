---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

<script language="javascript">
function callback(data) {
    var places = [];

    var activeIcon = L.icon({
            iconSize:    [25, 41],
            iconAnchor:  [12, 41],
            popupAnchor: [1, -34],
            tooltipAnchor: [16, -28],
            shadowSize:  [41, 41],
            iconUrl: 'marker-active.png',
            shadowUrl: 'marker-shadow.png',
            });
    var inactiveIcon = L.icon({
            iconSize:    [25, 41],
            iconAnchor:  [12, 41],
            popupAnchor: [1, -34],
            tooltipAnchor: [16, -28],
            shadowSize:  [41, 41],
            iconUrl: 'marker-inactive.png',
            shadowUrl: 'marker-shadow.png',});

    places = data['results'].map(r => L.marker([r['geometry']['coordinates']['1'], r['geometry']['coordinates']['0']], { icon: (r['status']['id'] == '3' ? inactiveIcon : activeIcon) }).bindPopup('<strong>' + (r['description'] || "Probe #" + r['id']) + '</strong><br />Status: ' + r['status']['name'] + '<br /><a href="https://atlas.ripe.net/probes/' + r['id'] + '/">Jump to probe page</a>'));

    var osm          = L.tileLayer('https://{s}.tile.openstreetmap.fr/osmfr/{z}/{x}/{y}.png', { attribution: 'Map data © <a href="https://www.openstreetmap.fr/">OpenStreetMap France</a>' }),
        pirates      = L.tileLayer('https://{s}.tiles.mapbox.com/v4/mapbox.pirates/{z}/{x}/{y}.png?access_token=pk.eyJ1Ijoic21vcnRleCIsImEiOiJjams2eDRlNngxeG9zM3BtcTZ3cWV2aGE1In0.QE3nSWsjqOcjcZIhnX7eXg', {attribution: 'Pirates © <a href="https://www.mapbox.com/about/maps/">Mapbox</a>'}),
        highcontrast = L.tileLayer('https://{s}.tiles.mapbox.com/v4/mapbox.high-contrast/{z}/{x}/{y}.png?access_token=pk.eyJ1Ijoic21vcnRleCIsImEiOiJjams2eDRlNngxeG9zM3BtcTZ3cWV2aGE1In0.QE3nSWsjqOcjcZIhnX7eXg', {attribution: 'Mapbox High Contrast © <a href="https://www.mapbox.com/about/maps/">Mapbox</a>'})
        toner = L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/toner/{z}/{x}/{y}.png', {attribution: 'Toner © <a href="https://maps.stamen.com/">Stamen Design</a>' })
            ;

    var layer = L.layerGroup(places);
    var mymap = L.map('map', {layers: [highcontrast, layer]});

    L.control.scale({maxWidth: 300}).addTo(mymap);
    var baseMaps = {
        "Mapbox High Contrast": highcontrast,
        "OpenStreetMap France": osm,
        "Pirate": pirates,
        "Toner": toner
    };
    L.control.layers(baseMaps, {}).addTo(mymap);

    a = [-24.806681, -156.577148]
    b = [-6.904614, -132.93457]
    mymap.fitBounds([a, b]);
}

var script = document.createElement('script');
script.src = 'https://atlas.ripe.net/api/v2/probes/?country_code=PF&format=jsonp';

document.getElementsByTagName('head')[0].appendChild(script);
</script>

