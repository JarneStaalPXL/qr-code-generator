<template>
    <div class="qr-code-generator">
        <h1>QR Code Generator</h1>
        <input v-model="url" placeholder="Enter URL here..." type="text">
        <button @click="generateQRCode">Generate QR Code</button>
        <div v-if="qrCodeDataUrl">
            <img :src="qrCodeDataUrl" alt="Generated QR Code" width="500" height="500" />
            <button @click="downloadImage">Download QR Code</button>
        </div>
    </div>
</template>

<script>
import QRCode from 'qrcode'

export default {
    name: 'QRCodeGenerator',
    data() {
        return {
            url: '',
            qrCodeDataUrl: null
        }
    },
    methods: {
        async generateQRCode() {
            try {
                this.qrCodeDataUrl = await QRCode.toDataURL(this.url);
            } catch (error) {
                console.error('Failed to generate QR Code:', error);
            }
        },
        downloadImage() {
            // Create a temporary link element
            const link = document.createElement('a');
            link.href = this.qrCodeDataUrl;
            // Set the download attribute with a default file name
            link.download = 'QRCode.png';
            // Append the link to the body
            document.body.appendChild(link);
            // Trigger the download by simulating click
            link.click();
            // Remove the link after downloading
            document.body.removeChild(link);
        }
    }
}
</script>

<style>
.qr-code-generator {
    text-align: center;
    margin-top: 50px;
}
</style>