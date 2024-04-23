<template>
    <div class="qr-code-generator">
        <h1>QR Code Generator</h1>
        <div class="inputcontainer">
            <input
                class="block rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                v-model="url" placeholder="Plak de link hier..." type="text" />
        </div>

        <button class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded"
            @click="generateQRCode">
            Genereer QR Code
        </button>
        <div v-if="qrCodeDataUrl" class="qrcodecontainer">
            <img :src="qrCodeDataUrl" alt="Generated QR Code" class="qrcode" />
            <button class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded"
                @click="downloadImage">
                Download QR Code
            </button>
        </div>
    </div>
</template>

<script>
import QRCode from "qrcode";

export default {
    name: "QRCodeGenerator",
    data() {
        return {
            url: "",
            qrCodeDataUrl: null,
        };
    },
    methods: {
        async generateQRCode() {
            const options = {
                errorCorrectionLevel: "H", // High error correction level
                type: "image/png",
                quality: 1, // Highest quality
                margin: 1,
                scale: 10, // Higher scale for better resolution
            };
            try {
                this.qrCodeDataUrl = await QRCode.toDataURL(this.url, options);
            } catch (error) {
                console.error("Failed to generate QR Code:", error);
            }
        },
        downloadImage() {
            const link = document.createElement("a");
            link.href = this.qrCodeDataUrl;
            link.download = "QRCode.png";
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        },
    },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&display=swap");

* {
    font-family: "Montserrat", sans-serif;
    box-sizing: border-box;
}

input,
button {
    width: 300px;
}

input {
    padding: 10px;
    ;
}

.inputcontainer {
    justify-content: center;
    display: flex;
    margin-top: 20px;
    margin-bottom: 50px;
}

.qr-code-generator {
    text-align: center;
    margin-top: 50px;
}

.qrcodecontainer {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

.qrcodecontainer img {
    margin-top: 20px;
    max-width: 300px;
    max-height: 300px;
    border: 1px solid #ccc;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    margin-bottom: 20px;
}

.qrcode {}
</style>
