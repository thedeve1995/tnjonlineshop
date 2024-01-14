<!-- App.vue -->
<template>
    <div class="setting">
        <div class="sideAdminNav">
            <button>Profil</button>
            <button>Cart</button>
            <button>On Progress</button>
            <button>Done</button>
            <button>Data</button>
        </div>
        <div>
            <form>
                <div>
                    <label for="name">Nama:</label>
                    <input type="text" id="name" v-model="nama" />
                </div>

                <!-- <div>
                <label for="email">Email:</label>
                <input type="email" id="email" v-model="formData.email" />
            </div> -->

                <div>
                    <label for="photo">Foto Profil:</label>
                    <input type="file" id="photo" @change="handleImageUpload" />

                </div>

                <button @click="uploadGambar">Simpan</button>
            </form>
        </div>
    </div>
</template>
  
<script setup>
import { ref } from 'vue';
import { getAuth, updateProfile } from "firebase/auth";
import { storage, db } from "@/firebase";
import {
    ref as storageRef,
    uploadBytes,
    getDownloadURL,
} from "firebase/storage";

const nama = ref("")
let selectedImageFile = null;
let imageRef;
const linkImage = ref("");

const handleImageUpload = (event) => {
    const file = event.target.files[0];
    if (file) {
        // Initialize imageRef within handleImageUpload
        imageRef = storageRef(storage, file.name);
        selectedImageFile = file;
    }
};

const uploadGambar = async () => {
    if (selectedImageFile) {
        // Upload the image to Firebase Storage
        await uploadBytes(imageRef, selectedImageFile);
        // Get the download URL of the uploaded image
        const imageUrl = await getDownloadURL(imageRef);
        // Update the linkImage field with the image URL
        linkImage.value = imageUrl;
    }

    updateProfile(auth.currentUser, {
        displayName: nama.value,
        photoURL: linkImage.value,
    }).then(() => {
        console.log("Profile updated!")
    }).catch((error) => {
        // An error occurred
        // ...
    });
};

const auth = getAuth();

</script>

  
<style scoped>
/* Tambahkan gaya CSS sesuai kebutuhan Anda */
form {
    max-width: 400px;
    margin: auto;
}

label {
    display: block;
    margin-bottom: 8px;
}
</style>
  