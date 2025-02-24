<script setup>
const supabase = useSupabaseClient()
const categories = ref([])
const isLoading = ref(false)
const form = ref({
    nama_pengambil: "",
    nama_barang: "",
    id_kategori: "",
    jumlah_pengambilan: 0,
})

const getCategory = async () => {
    const { data, error } = await supabase.from('kategori').select('*');
    
    if (error) {
        console.error("❌ Error mengambil kategori:", error);
    } else {
        console.log("✅ Kategori berhasil diambil:", data); // Debugging
        categories.value = data;
    }
};

async function saveToTable() {
    // console.log("🔍 Selected id_kategori:", form.value.id_kategori); // Cek nilai sebelum insert

    // if (!form.value.id_kategori) {
    //     alert("⚠️ Pilih kategori terlebih dahulu!");
    //     return;
    // }

    // try {
    //     const { data, error } = await supabase.from('riwayat')
    //         .insert([{
    //             nama_pengambil: form.value.nama_pengambil,
    //             nama_barang: form.value.nama_barang,
    //             id_kategori: Number(form.value.id_kategori),                
    //             jumlah_pengambilan: form.value.jumlah_pengambilan,
    //         }]);

    //     if (error) throw error;

    //     console.log("✅ Success: Data saved", data);
    //     navigateTo("/dashboard/");
    // } catch (err) {
    //     console.error("❌ Error saving data:", err.message);
    //     alert("Gagal menyimpan data: " + err.message);
    // }

        const idKategori = parseInt(form.value.id_kategori, 10);
        const { data, error } = await supabase.from('riwayat')
            .insert([{
                nama_pengambil: form.value.nama_pengambil,
                nama_barang: form.value.nama_barang,
                id_kategori: idKategori,
                jumlah_pengambilan: form.value.jumlah_pengambilan,
            }])
        if (error) throw error
        if (data) {
            console.log("succes: saved to table")
        }
    if (error) throw error
    else {
        isLoading.value = false;
        navigateTo("/dashboard/") 
    }
}

// watch(() => form.value.id_kategori, (newVal) => {
//     console.log("🔍 id_kategori berubah:", newVal);
// });

onMounted(() => {
    getCategory()
})


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
                            <form @submit.prevent="saveToTable" class="m-5">
                                <div class="mb-3">
                                    <label class="form-label">Nama Pengambil</label>
                                    <input v-model="form.nama_pengambil" type="text" class="form-control">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Nama Barang</label>
                                    <input v-model="form.nama_barang" type="text" class="form-control">
                                </div>
                                
                                <div class="mb-3">
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
                                </div>
                                <!-- <p>🔍 Selected id_kategori: {{ form.id_kategori }}</p> -->
                                <div class="mb-3">
                                    <label class="form-label">Jumlah</label>
                                    <input v-model="form.jumlah_pengambilan" type="number" class="form-control"  aria-describedby="emailHelp">
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

h3, label, input, .btn {
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
