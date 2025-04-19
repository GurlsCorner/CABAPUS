<script setup>
let year = new Date().getFullYear() 
</script>

<template>
    <div class="footer text-center bottom-0">
        <p class="p-2 m-0">© {{ year }}. CABAPUS. All rights reserved.</p>
    </div>
</template>

<style scoped>
p {
    font-family: "Josefin Sans", serif;
    /* margin-top: 1rem; */
}
.footer {
    background-color: #9AA6B2;
}
</style>