<script setup>
const supabase = useSupabaseClient()
const categories = ref([])
const kategori = ref()
const files = ref()
const url_img = `https://ypbajejglnixqrikfvyp.supabase.co/storage/v1/object/public/fotoBarang`
const form = ref({
    nama_barang: "",
    id_kategori: "1",
    jumlah: "",
    foto_barang: null
})

const isLoading = ref(false)

const getCategory = async () => {
    const { data, error } = await supabase.from('kategori').select('*')
    if (data) categories.value = data
}


async function saveToTable() {
    let file = files.value[0]
    const fileExt = file.name.split('.').pop()
    const fileName = `${Math.random()}.${fileExt}`
    const filePath = `ATK/${fileName}`
    isLoading.value = true

    const { data, error } = await supabase.storage.from('fotoBarang').upload(filePath, file)
    if (data) {
        console.log("upload success")
        const { data, error } = await supabase.from('barang')
            .insert([{
                nama_barang: form.value.nama_barang,
                id_kategori: "1",
                jumlah: form.value.jumlah,
                foto_barang: url_img + '/' + filePath
            }])
        if (error) throw error
        if (data) {
            console.log("succes: saved to table")
        }
    }
    if (error) throw error
    else {
        isLoading.value = false;
        navigateTo("/barangATK/") 
    }
}

function disableInput(input) {
  if (input.files.length > 0) {
    input.disabled = true; // Menonaktifkan input setelah gambar dipilih
  }
}



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
                <h3>Tambah Barang ATK</h3>
                <div class="row justify-content-center m-0">
                    <div class="col-10 col-md-7">
                        <div class="card shadow rounded-4 m-5">
                            <form @submit.prevent="saveToTable" class="m-5">
                                <div class="mb-3">
                                    <label class="form-label">Upload Gambar Disini</label>
                                    <input type="file" id="imageInput" @change="(e) => files = e.target.files" accept="image/*">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Nama Barang</label>
                                    <input v-model="form.nama_barang" type="text" class="form-control">
                                </div>
                                <!-- <div class="mb-3">
                                    <label for="kategori">Kategori File</label>
                                    <select v-model="kategori" class="form-control form-select">
                                        <option value="ATK">ATK</option>
                                        <option value="ART">ART</option>
                                    </select>
                                </div>
                                <div class="mb-3 kategori">
                                    <label for="kategori">Kategori</label>
                                    <select v-model="form.id_kategori" class="form-control form-select" id="kategori">
                                        <option v-for="(category, i) in categories" :key="i" :value="category.id_kategori">{{
                                            category.nama }}</option>
                                    </select>
                                </div> -->
                                <div class="mb-3">
                                    <label class="form-label">Jumlah</label>
                                    <input v-model="form.jumlah" type="number" class="form-control"  aria-describedby="emailHelp">
                                </div>
                                <div class="add text-center mt-5">
                                    <button type="submit" class="btn rounded-5">Tambah</button>
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

    .add {
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

    input, .form-select {
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
                <h3>Tambah Barang ATK</h3>
                <div class="row justify-content-center">
                    <div class="col-10">
                        <div class="card shadow rounded-4 m-5">
                            <form class="m-5">
                                <div class="mb-3">
                                    <label class="form-label">Nama Barang</label>
                                    <input type="text" class="form-control">
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



<!-- <template>
    <div class="container-fluid mb-5">
        <div class="page d-flex">
                <Sidebar />
            <div class="content mb-5">
                <h3>Tambah Barang ATK</h3>
                <div class="row justify-content-center">
                    <div class="col-10">
                        <div class="card shadow rounded-4 m-5">
                            <form class="m-5">
                                <div class="mb-3">
                                    <label class="form-label">Nama Barang</label>
                                    <input type="text" class="form-control">
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

h3, label, input, .btn {
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
    margin-left: 20rem; /* Offset the main content to the right of the sidebar */
    flex-grow: 1;
    margin-top: 5rem;
}

</style> -->