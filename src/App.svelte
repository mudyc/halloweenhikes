<style>
    #mapid {
        position: absolute;
        width: 100%;
        height: 100%;
    }

    :global(.leaflet-tile-container img) {
        -webkit-filter: grayscale(100%); /* Safari 6.0 - 9.0 */
        filter: grayscale(60%) brightness(107%) contrast(128%)
    }
</style>

<div id="mapid"></div>

<script>
import { onMount } from 'svelte'

let map;


const loadRoutes = async () => {
    const mapData = await fetch(`./routes.json`, {
            method: 'GET',
    })
	const routes = await mapData.json()
	for (const route of routes) {
        new L.GPX(route.file, {
            async: true,
            gpx_options: {
                parseElements: ['track', 'route']
            }
        }).on('loaded', function(e) {
			//map.fitBounds(e.target.getBounds());
		}).addTo(map);
	}
    console.log(routes)
}

onMount(async () => {
    map = L.map('mapid').setView([65.330, 26.818], 5);
    L.tileLayer('//{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    }).addTo(map);
    L.tileLayer('//tile.waymarkedtrails.org/hiking/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://waymarkedtrails.org">Waymarked Trails</a>'
    }).addTo(map);
    
    const jkl = [62.24452234218559, 25.748742842059354];
    const circle = L.circle(jkl, {
        radius: 150*1000, fill: false
    })
    circle.addTo(map);
    map.fitBounds(circle.getBounds());

    await loadRoutes()
})
</script>


