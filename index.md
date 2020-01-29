---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

<script language="javascript">
function probeId(probe) {
    return probe['id'];
}
function probeRawTitle(probe) {
    return "Probe #" + probeId(probe);
}
function probeTitle(probe) {
    if (probe['description']) {
        return probe['description'] + " (" + probeRawTitle(probe) + ")";
    } else {
        return probeRawTitle(probe);
    }
}
function probeUri(probe) {
    return 'https://atlas.ripe.net/probes/' + probe['id'] + '/';
}
function probeStatus(probe) {
    return "Status: " + probe['status']['name'];
}
function probeLink(probe) {
    return '<a href="' + probeUri(probe) + '" target="_blank">' + probeRawTitle(probe) + ' page on RIPE Atlas</a>';
}
function probePopup(probe) {
    return '<strong>' + probeTitle(probe) + '</strong><br />' + probeStatus(probe) + '<br />' + probeLink(probe);
}
function callback(data) {
    var active_probes = [];
    var inactive_probes = [];
    var abandoned_probes = [];

    var active_probe_icon = L.icon({
        iconSize:      [25, 41],
        iconAnchor:    [12, 41],
        popupAnchor:   [1, -34],
        tooltipAnchor: [16, -28],
        shadowSize:    [41, 41],
        iconUrl:       'marker-active.png',
        shadowUrl:     'marker-shadow.png',
    });
    var inactive_probe_icon = L.icon({
        iconSize:      [25, 41],
        iconAnchor:    [12, 41],
        popupAnchor:   [1, -34],
        tooltipAnchor: [16, -28],
        shadowSize:    [41, 41],
        iconUrl:       'marker-inactive.png',
        shadowUrl:     'marker-shadow.png',
    });
    var abandoned_probe_icon = L.icon({
        iconSize:      [25, 41],
        iconAnchor:    [12, 41],
        popupAnchor:   [1, -34],
        tooltipAnchor: [16, -28],
        shadowSize:    [41, 41],
        iconUrl:       'marker-abandoned.png',
        shadowUrl:     'marker-shadow.png',
    });
    var ambassador_probe_icon = L.icon({
        iconSize:      [25, 41],
        iconAnchor:    [12, 41],
        popupAnchor:   [1, -34],
        tooltipAnchor: [16, -28],
        shadowSize:    [41, 41],
        iconUrl:       'marker-ambassador.png',
        shadowUrl:     'marker-shadow.png',
    });

    var ambassador_probe_ids = [
        52614,
        52814,
        53286,
    ];

    data['results'].forEach(r => {
        if (r['status']['id'] == '3') {
            abandoned_probes.push(L.marker([r['geometry']['coordinates']['1'], r['geometry']['coordinates']['0']], { icon: abandoned_probe_icon }).bindPopup(probePopup(r)));
        } else if (r['status']['id'] == '2') {
            inactive_probes.push(L.marker([r['geometry']['coordinates']['1'], r['geometry']['coordinates']['0']], { icon: inactive_probe_icon }).bindPopup(probePopup(r)));
        } else {
            var icon = active_probe_icon;
            if (ambassador_probe_ids.includes(probeId(r)))
                icon = ambassador_probe_icon;

            active_probes.push(L.marker([r['geometry']['coordinates']['1'], r['geometry']['coordinates']['0']], { icon: icon }).bindPopup(probePopup(r)));
        }
    });

    var mapsurfer    = L.tileLayer('https://api.openrouteservice.org/mapsurfer/{z}/{x}/{y}.png?api_key={orskey}', {attribution: 'Map data © <a href="https://maps.openrouteservice.org/">GIScience Research Group @ Heidelberg University</a>', orskey: '5b3ce3597851110001cf6248e6eeebc5abbe4825b1b19b3a321b7a15'});
    var osm          = L.tileLayer('https://{s}.tile.openstreetmap.fr/osmfr/{z}/{x}/{y}.png', { attribution: 'Map data © <a href="https://www.openstreetmap.fr/">OpenStreetMap France</a>' });
    var pirates      = L.tileLayer('https://{s}.tiles.mapbox.com/v4/mapbox.pirates/{z}/{x}/{y}.png?access_token=pk.eyJ1Ijoic21vcnRleCIsImEiOiJjams2eDRlNngxeG9zM3BtcTZ3cWV2aGE1In0.QE3nSWsjqOcjcZIhnX7eXg', {attribution: 'Pirates © <a href="https://www.mapbox.com/about/maps/">Mapbox</a>'});
    var highcontrast = L.tileLayer('https://{s}.tiles.mapbox.com/v4/mapbox.high-contrast/{z}/{x}/{y}.png?access_token=pk.eyJ1Ijoic21vcnRleCIsImEiOiJjams2eDRlNngxeG9zM3BtcTZ3cWV2aGE1In0.QE3nSWsjqOcjcZIhnX7eXg', {attribution: 'Mapbox High Contrast © <a href="https://www.mapbox.com/about/maps/">Mapbox</a>'});
    var toner        = L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/toner/{z}/{x}/{y}.png', {attribution: 'Toner © <a href="https://maps.stamen.com/">Stamen Design</a>' });

    var markers_layer = L.markerClusterGroup();

    var active_probes_layer   = L.featureGroup.subGroup(markers_layer, active_probes);
    var inactive_probes_layer = L.featureGroup.subGroup(markers_layer, inactive_probes);
    var abandoned_probes_layer = L.featureGroup.subGroup(markers_layer, abandoned_probes);

    markers_layer.addLayer(active_probes_layer, inactive_probes_layer);

    var map = L.map('map', {layers: [mapsurfer, markers_layer, active_probes_layer, inactive_probes_layer]});

    L.control.scale({maxWidth: 300}).addTo(map);

    var base_layers = {
        'MapSurfer':            mapsurfer,
        'Mapbox High Contrast': highcontrast,
        'OpenStreetMap France': osm,
        'Pirate':               pirates,
        'Toner':                toner
    };
    var overlays = {
        'Active probes':     active_probes_layer,
        'Inactive probes':   inactive_probes_layer,
        'Abandoned probles': abandoned_probes_layer,
    };
    L.control.layers(base_layers, overlays).addTo(map);

    a = [-30, -158]
    b = [-7, -132]
    map.fitBounds([a, b]);
}

var script = document.createElement('script');
script.src = 'https://atlas.ripe.net/api/v2/probes/?country_code=PF&format=jsonp';

document.getElementsByTagName('head')[0].appendChild(script);
</script>

