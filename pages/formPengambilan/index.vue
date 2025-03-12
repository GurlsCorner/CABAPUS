<script setup>
const supabase = useSupabaseClient()
const categories = ref([])
const isLoading = ref(false)
const searchQuery = ref('');
const filteredItems = ref([]);
const selectedItem = ref(null);
const formData = ref({
    nama_pengambil: '',
    jumlah_pengambilan: 1,
});
// const form = ref({
//     nama_pengambil: "",
//     nama_barang: "",
//     id_kategori: "",
//     jumlah_pengambilan: 0,
// })

// const getCategory = async () => {
//     const { data, error } = await supabase.from('kategori').select('*');

//     if (error) {
//         console.error("❌ Error mengambil kategori:", error);
//     } else {
//         console.log("✅ Kategori berhasil diambil:", data); // Debugging
//         categories.value = data;
//     }
// };


// Ambil barang dari Supabase berdasarkan kata kunci
const fetchBarang = async () => {
    if (!searchQuery.value || searchQuery.value.length === 0) {
        filteredItems.value = [];
        return;
    }

    const { data, error } = await supabase
        .from('barang')
        .select(`*, kategori(*)`)
        .ilike('nama_barang', `%${searchQuery.value}%`)
        .order('nama_barang', { ascending: true });

    if (!error) {
        console.log("✅ Data barang berhasil diambil:", data); // Debugging
        filteredItems.value = data;
    } else {
        console.error("❌ Error saat fetch barang:", error);
    }
};



// Pantau perubahan input
watch(searchQuery, fetchBarang);

// Pilih barang dari dropdown
const selectBarang = (barang) => {
    if (!barang) return;
    
    console.log('Barang dipilih:', barang); // Debugging

    selectedItem.value = { 
        id: barang.id, 
        id_barang: barang.id, 
        id_kategori: barang.id_kategori || barang.kategori?.id, // Pastikan ambil ID kategori
        nama_barang: barang.nama_barang
    };

    searchQuery.value = barang.nama_barang; // Masukkan nama barang ke input
    filteredItems.value = []; // Kosongkan dropdown setelah memilih barang
};

// Kirim data ke Supabase
const submitForm = async () => {
    if (!selectedItem.value || !formData.value.nama_pengambil || formData.value.jumlah_pengambilan <= 0) {
        console.error("⚠️ Data tidak lengkap!", { selectedItem: selectedItem.value, formData: formData.value });
        return;
    }

    const { error } = await supabase.from('riwayat').insert([
        {
            id_barang: selectedItem.value.id_barang, // Harus pakai ID barang yang benar
            nama_pengambil: formData.value.nama_pengambil,
            id_kategori: selectedItem.value.id_kategori,
            jumlah_pengambilan: formData.value.jumlah_pengambilan,
        },
    ]);

    if (error) {
        console.error("❌ Error saat insert ke Supabase:", error);
        return;
    }

    if (!error) navigateTo('/riwayat')

    // console.log("✅ Data berhasil dikirim!");
    // alert('Data berhasil dikirim');

    // Reset form
    searchQuery.value = '';
    selectedItem.value = null;
    formData.value = { nama_pengambil: '', jumlah_pengambilan: 1 };
};





// watch(() => form.value.id_kategori, (newVal) => {
//     console.log("🔍 id_kategori berubah:", newVal);
// });

// onMounted(() => {
//     getCategory()
// })


definePageMeta({
    layout: 'admin',
    middleware: 'auth'
})


</script>
<template>
    <div class="container-fluid">
        <div class="page d-flex">
            <Sidebar />
            <div class="content mb-5">
                <h3>Form Pengambilan Barang</h3>
                <div class="row justify-content-center">
                    <div class="col-10 col-md-7">
                        <div class="card shadow rounded-4 m-5">
                            <form @submit.prevent="submitForm" class="m-5">
                                <div class="mb-3">
                                    <label class="form-label">Nama Pengambil</label>
                                    <input v-model="formData.nama_pengambil" type="text" class="form-control">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Nama Barang</label>
                                    <input v-model="searchQuery" type="text" class="form-control" @focus="fetchBarang"
                                        @input="fetchBarang">
                                    <!-- Dropdown Hasil Pencarian -->
                                    <ul v-if="filteredItems.length > 0 && searchQuery !== selectedItem?.nama_barang"
                                        class="dropdown">
                                        <li v-for="barang in filteredItems" :key="barang.id"
                                            @click="selectBarang(barang)">
                                            {{ barang.nama_barang }}
                                        </li>
                                    </ul>
                                </div>
                                <!-- Menampilkan Barang yang Dipilih -->
                                <!-- <p v-if="selectedItem?.nama_barang">Barang Dipilih: {{ selectedItem?.nama_barang }}</p> -->


                                <!-- <div class="mb-3">
                                    <label class="form-label">Jenis Barang</label>
                                    <div class="d-flex gap-5">
                                        <div v-for="category in categories" :key="category.id">
                                            <input
                                                type="radio"
                                                :id="'category-' + category.id"
                                                v-model="form.id_kategori"
                                                :value="category.id"
                                                name="kategori"
                                            />
                                            <label :for="'category-' + category.id">{{ category.nama }}</label>
                                        </div>
                                    </div>
                                </div> -->
                                <!-- <p>🔍 Selected id_kategori: {{ form.id_kategori }}</p> -->
                                <div class="mb-3">
                                    <label class="form-label">Jumlah</label>
                                    <input v-model="formData.jumlah_pengambilan" type="number" class="form-control"
                                        aria-describedby="emailHelp">
                                </div>
                                <div class="submit text-center mt-5">
                                    <button type="submit" class="btn rounded-5">Kirim</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');

h3,
label,
input,
.btn {
    font-family: "Josefin Sans", serif;
}

h3 {
    margin-left: 2rem;
    margin-top: 4rem;
}

.page {
    display: flex;
}

.card {
    width: 80%;
    margin: auto;
}

.btn {
    width: 15rem;
    color: #fff;
    background-color: #9AA6B2;
}

.content {
    margin-left: 20rem;
    flex-grow: 1;
    margin-top: 5rem;
}

ul {
    list-style: none;
}

@media only screen and (max-width: 600px) {
    h3 {
        margin-left: 1rem;
        margin-top: 5rem;
        font-size: 1rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }

    .card {
        width: 100%;
        margin: 2rem 0 !important;
    }

    .submit {
        margin-top: 2rem !important;
    }

    .btn {
        padding-top: 0.4rem;
        width: 7rem;
        height: 1.7rem;
        font-size: 0.7rem;
    }

    form {
        margin: 2.5rem !important;
        font-size: 0.7rem;
    }

    .form-control {
        height: 1.5rem;
    }

    input {
        font-size: 0.7rem;
    }

}

@media only screen and (min-width: 600px) and (max-width: 890px) {
    h3 {
        margin-left: 2rem;
        margin-top: 5.5rem;
        font-size: 1.3rem;
    }

    .content {
        margin-left: 6rem;
        margin-top: 3rem;
    }

    .card {
        width: 100%;
        margin: 2rem 0 !important;
    }

    .submit {
        margin-top: 2rem !important;
    }

    .btn {
        padding-top: 0.4rem;
        width: 7rem;
        height: 1.7rem;
        font-size: 0.7rem;
    }

    form {
        margin: 2.5rem !important;
        font-size: 0.7rem;
    }

    .form-control {
        height: 1.5rem;
    }

    input {
        font-size: 0.7rem;
    }

}
</style>


<!-- <template>
    <div class="container-fluid">
        <div class="dashboard d-flex">
            <Sidebar />
            <div class="content mb-5">   
                <h3>Form Pengambilan Barang</h3>
                <div class="row justify-content-center">
                    <div class="col-10">
                        <div class="card shadow rounded-4 m-5">
                            <form class="m-5">
                                <div class="mb-3">
                                    <label class="form-label">Nama Pengambil</label>
                                    <input type="text" class="form-control">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Nama Barang</label>
                                    <input type="text" class="form-control">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Jenis Barang</label>
                                    <div class="d-flex gap-5">
                                        <div class="form-check">
                                            <input class="form-check-input" type="radio" name="flexRadioDefault" id="flexRadioDefault1">
                                            <label class="form-check-label">
                                                ATK
                                            </label>
                                            </div>
                                        <div class="form-check">
                                            <input class="form-check-input" type="radio" name="flexRadioDefault" id="flexRadioDefault2">
                                            <label class="form-check-label">
                                                ART
                                            </label>
                                        </div>
                                    </div>
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Jumlah</label>
                                    <input type="number" class="form-control"  aria-describedby="emailHelp">
                                </div>
                                <div class="text-center mt-5">
                                    <button type="submit" class="btn rounded-5">Kirim</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
             </div>
        </div>
    </div>
</template> 

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100..700;1,100..700&display=swap');

h3, label, .btn {
    font-family: "Josefin Sans", serif;
}

h3 {
    margin-left: 2rem;
    margin-top: 4rem;
}

.card {
    width: 80%;
    margin: auto;
}

.btn {
    width: 15rem;
    color: #fff;
    background-color: #9AA6B2;
}

.content {
    margin-left: 20rem;
    flex-grow: 1;
    margin-top: 5rem;
}

@media only screen and (max-width: 600px) {
    h3 {
        margin-left: 1.5rem;
        margin-top: 5rem;
    }

    .content {
        margin-left: 7rem;
        margin-top: 3rem;
    }

    .card {
        width: 100%;
        margin: 2rem 0 !important;
    }

    .btn {
        width: 10rem;
    }

    form {
        margin: 2.5rem !important;
    }
}

</style> -->
