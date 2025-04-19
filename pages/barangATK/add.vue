<script setup>
const supabase = useSupabaseClient()
const categories = ref([])
const satuan = ref([])
const files = ref()
const url_img = `https://ypbajejglnixqrikfvyp.supabase.co/storage/v1/object/public/fotoBarang`
const form = ref({
    nama_barang: "",
    spek: "",
    id_kategori: "1",
    jumlah: "",
    id_satuan: "",
    foto_barang: null
})

const isLoading = ref(false)

const getCategory = async () => {
    const { data, error } = await supabase.from('kategori').select('*')
    if (data) categories.value = data
}

const getSatuan = async () => {
    const { data, error } = await supabase.from('satuan').select('*')
    if (data) satuan.value = data
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
                spek: form.value.spek,
                id_kategori: "1",
                jumlah: form.value.jumlah,
                id_satuan: form.value.id_satuan,
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

onMounted(() => {
    getCategory()
    getSatuan()
})


definePageMeta({
    layout: 'admin',
    middleware: 'auth'
})

useHead({ title: "Tambah Barang ATK - CABAPUS" })


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
                                <div class="mb-3">
                                    <label class="form-label">Spek</label>
                                    <input v-model="form.spek" type="text" class="form-control">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Jumlah</label>
                                    <input v-model="form.jumlah" type="number" class="form-control"  aria-describedby="emailHelp">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Satuan</label>
                                    <select v-model="form.id_satuan" class="form-control form-select" id="keperluan">
                                        <option v-for="(satu, i) in satuan" :key="i" :value="satu.id_satuan">{{ satu.nama }}
                                        </option>
                                    </select>
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