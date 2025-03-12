<script setup>

const supabase = useSupabaseClient()
const route = useRoute()
const barangID = Number(route.params.id)
const categories = ref([])
const imgBarang = ref()
const barangART = ref({
    nama_barang: "",
    id_kategori: null,
    jumlah: "",
    foto_barang: null
})


const getCategory = async () => {
    const { data, error } = await supabase.from('kategori').select('*')
    if (data) categories.value = data
}

const getBarangId = async () => {
    let { data, error } = await supabase
    .from('barang')
    .select(`*, kategori (*)`)
    .eq('id', barangID)
        .single()
        
        if (error) {
            console.log(this.$route
        ); 
        return
    }

    console.log("Barang ditemukan:", data)

    if (data.foto_barang) 
        data.foto_barangPath = data.foto_barang.substr(data.foto_barang.indexOf(data.kategori.nama)) 

        barangART.value = {
        ...data,
        id_kategori: data.kategori?.id || data.id_kategori
    };
}


async function imgPicked(e) {
    const file = e.target.files[0]
    imgBarang.value = file
    const { data: category, error } = await supabase.from('kategori')
        .select('nama, id')
        .eq('id', barangART.value.kategori?.id || barangART.value.id_kategori)
        .single();
        
    if (error) throw error;

    if (category && category.nama) {
        barangART.value.foto_barangPath = `${category.nama}/${file.name}`;
        barangART.value.id_kategori = category.id; 
    } else {
        console.error("Kategori tidak ditemukan atau tidak memiliki nama!");
    }
}

    const updateLoading = ref(false)

    async function updateBarangATK() {
    try {
        updateLoading.value = true;

        let newFoto = barangART.value.foto_barangPath;
        let oldFoto = barangART.value.foto_barang?.substr(barangART.value.foto_barang?.indexOf('fotoBarang'));
        
        
        // Jika ada gambar baru, lakukan update di storage
        if (imgBarang.value) {
            if (newFoto === oldFoto) {
                const { error } = await supabase.storage.from('fotoBarang').update(newFoto, imgBarang.value);
                if (error) throw error;
            } else {
                const { error } = await supabase.storage.from('fotoBarang').upload(newFoto, imgBarang.value);
                if (error) throw error;
                if (oldFoto) {
                    const { error } = await supabase.storage.from('fotoBarang').remove(oldFoto);
                    if (error) throw error;
                }
            }
        }

        const { data: { publicUrl: url } } = supabase.storage.from('fotoBarang').getPublicUrl(newFoto);


        // Update database barang
        const { error } = await supabase.from('barang').update({
            nama_barang: barangART.value.nama_barang,
            id_kategori: barangART.value.id_kategori,
            jumlah: barangART.value.jumlah,
            foto_barang: url
        }).eq('id', barangID);

        if (error) throw error;

        console.log("Barang Berhasil Diperbarui!");
        navigateTo('/barangART');

    } catch (error) {
        console.error(error);
    } finally {
        updateLoading.value = false;
    }
}


watch(() => route.params.id, (newID) => {
    if (newID) {
        console.log("ID baru ditemukan:", newID)
        getBarangId()
    }
}, { immediate: true }) // Agar dijalankan langsung saat halaman dimuat


function cekKategori() {
    console.log("🔄 ID Kategori setelah dipilih:", barangART.value.id_kategori);
}

onMounted(() => {
    getBarangId()    
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
                <h3>Edit Barang ART </h3>
                <div class="row justify-content-center">
                    <div class="col-10 col-md-7">
                        <div class="card shadow rounded-4 m-5">
                            <form @submit.prevent="updateBarangATK" class="m-5">
                                <div class="mb-3">
                                    <img :src="barangART.foto_barang" alt="img-barang" class="w-50"> <br>
                                    <label class="form-label">Upload Gambar Disini</label>
                                    <input :disabled="!barangART.id_kategori" @change="imgPicked" type="file" accept="image/*">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Nama Barang</label>
                                    <input v-model="barangART.nama_barang"  type="text" class="form-control">
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Kategori</label>
                                    <select v-model="barangART.kategori" @change="cekKategori" :key="barangART.id_kategori" class="form-control form-select" id="keperluan">
                                        <option v-for="category in categories" :key="category.id" :value="category">
                                            {{ category.nama }}</option>
                                    </select>
                                </div>
                                <div class="mb-3">
                                    <label class="form-label">Jumlah</label>
                                    <input v-model="barangART.jumlah" type="number" class="form-control"  aria-describedby="emailHelp">
                                </div>
                                <div class="add text-center mt-5">
                                    <button type="submit" class="btn rounded-5">Edit</button>
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