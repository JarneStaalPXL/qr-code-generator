<template>
    <div class="qr-code-generator">
        <h1>QR Code Generator</h1>
        <div class="inputcontainer">
            <input
                class="block rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
                v-model="url" placeholder="Plak de link hier..." type="text" @keydown.enter="generateQRCode()" />



        </div>

        <div v-if="url.length > 1033" class="error-message text-red-600">
            De link is te lang.
        </div>

        <button class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded genDownBtn"
            @click="generateQRCode">
            Genereer QR Code
        </button>
        <div v-if="qrCodeDataUrl" class="qrcodecontainer">
            <img :src="qrCodeDataUrl" alt="Generated QR Code" class="qrcode" />
            <button class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded genDownBtn"
                @click="downloadImage">
                Download QR Code
            </button>
        </div>


        <h1 class="font-bold historyTitle" v-if="savedQRs.length > 0">Geschiedenis</h1>
        <div class="savedQrCodes" v-if="savedQRs.length > 0">
            <table class="table-auto">
                <thead>
                    <tr>
                        <th class="px-4 py-2 hideColumn">QR Code</th>
                        <th class="px-4 py-2">URL</th>
                        <th class="px-4 py-2">Actie</th>
                        <th class="px-4 py-2 hideColumn">Verwijder</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(item, index) in reversedSavedQRs" :key="index">
                        <td class="border px-4 py-2 hideColumn">
                            <img :src="item.dataUrl" alt="Saved QR Code" class="qrcode" />
                        </td>
                        <td class="border px-4 py-2" style="max-width: 200px;word-wrap: break-word;">
                            {{ item.url }}
                        </td>
                        <td class="border px-4 py-2">
                            <button @click="downloadImageFromElement(item)"
                                class="bg-indigo-600 hover:bg-indigo-700 text-white rounded downloadBtnTable">Download
                                QR Code</button>
                        </td>
                        <td class="border px-4 py-2 hideColumn">
                            <button @click="removeItemFromSavedQRs(item);"
                                class="bg-red-600 hover:bg-red-700 text-white rounded downloadBtnTable">Verwijder</button>
                        </td>
                    </tr>
                </tbody>

            </table>
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
            savedQRs: [],
        };
    },
    created() {
        this.loadSavedQRs();
    },
    computed: {
        reversedSavedQRs() {
            // Return a reversed copy of the saved QRs array
            return [...this.savedQRs].reverse();
        }
    },
    methods: {
        removeItemFromSavedQRs(item) {
            this.savedQRs = this.savedQRs.filter((qr) => qr.url !== item.url);
            localStorage.setItem("qrCodes", JSON.stringify(this.savedQRs));
        },
        async generateQRCode() {

            const options = {
                errorCorrectionLevel: "H",
                type: "image/png",
                quality: 1,
                margin: 1,
                scale: 10,
            };
            try {
                this.qrCodeDataUrl = await QRCode.toDataURL(this.url, options);
                this.saveQR();
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
        downloadImageFromElement(el) {
            const link = document.createElement("a");
            link.href = el.dataUrl;
            link.download = el.url + "-qrcode.png";
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        },
        saveQR() {

            const qrData = { url: this.url, dataUrl: this.qrCodeDataUrl };

            // Save the QR code data to local storage if it doesn't exist
            if (!this.savedQRs.some((item) => item.url === this.url)) {
                this.savedQRs.push(qrData);
                localStorage.setItem("qrCodes", JSON.stringify(this.savedQRs));
            }
        },
        loadSavedQRs() {
            const savedData = localStorage.getItem('qrCodes');
            if (savedData) {
                this.savedQRs = JSON.parse(savedData);
            }
        }
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
.genDownBtn {
    width: 300px;
}

input {
    padding: 10px;
    ;
}

.error-message {
    margin: 20px;
}

.inputcontainer {
    justify-content: center;
    display: flex;
    margin-top: 20px;
    margin-bottom: 20px;
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

.qrcode {
    max-width: 100px;
    max-height: 100px;
    border: 1px solid #ccc;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.historyTitle {
    margin-top: 100px;
    margin-bottom: 20px;
    font-size: 25px;
}

.savedQrCodes {
    display: flex;
    justify-content: center;

    margin-bottom: 300px;

}

.downloadBtnTable {
    padding: 10px;
}

@media only screen and (max-width: 600px) {
    .hideColumn {
        display: none;
    }
}
</style>
