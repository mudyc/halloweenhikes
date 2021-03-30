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
		new L.GPX(route.file, {async: true}).on('loaded', function(e) {
			map.fitBounds(e.target.getBounds());
		}).addTo(map);
	}
    console.log(routes)
}

onMount(async () => {
    map = L.map('mapid').setView([65.330, 26.818], 5);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
    }).addTo(map);
    await loadRoutes()
})
</script>


