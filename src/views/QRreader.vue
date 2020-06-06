<template>
    <div class="home">
        <h1>Trying to follow you </h1>
        <div id="qr-reader" class="map">
			<qrcode-stream @decode="qrDecodeString"></qrcode-stream>
        </div>
    </div>
</template>

<script>
// @ is an alias to /src

// *********** //
// - QrcodeStream accesses the device camera and continuously scans incoming frames.
// - QrcodeDropZone renders to an empty region where you can drag-and-drop images to be decoded.
// - QrcodeCapture is a classic file upload field, instantly scanning all files you select.

import { QrcodeStream } from 'vue-qrcode-reader'


export default {
    name: 'QRreader',
	components: {
		QrcodeStream,
	},
    props: {
    },
    data() {
        return {
        }
    },
    async mounted() {
    },

    methods: {
        qrDecodeString(decodedString) {
            alert('Eu li: "' + decodedString + '" do código');
        }
    }
}
</script>

